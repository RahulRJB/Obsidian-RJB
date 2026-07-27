
# Python-WebServer


DATE:  15-07-26


Tags: [[Notes/python|python]] [[Notes/asyncio|asyncio]] [[backend]] [[webservers]]

# References:




# WSGI vs ASGI: The Python Web Server Landscape:

> [!abstract] Core Idea
> Every Python web app needs three layers working together: a **framework** (your code), a **protocol/interface** (the contract between framework and server), and a **server** (the process that actually accepts network connections). Confusion in this space almost always comes from mixing these three layers up.

---

## 1. The Three Layers

```
Client Request
      ↓
[SERVER]     — accepts TCP connections, parses HTTP  → e.g. Uvicorn, Gunicorn
      ↓
[INTERFACE]  — the calling convention/contract        → WSGI or ASGI
      ↓
[FRAMEWORK]  — your application logic, routing        → e.g. Flask, FastAPI, Django
```

A server without a framework has nothing to run. A framework without a server has no way to receive traffic. The interface (WSGI/ASGI) is just the *agreed-upon function signature* that lets any compliant server talk to any compliant framework — this is why you can swap Uvicorn for Hypercorn under FastAPI without changing your app code.

Related: [[Client-Server Architecture]], [[HTTP Protocol]]

---

## 2. Synchronous vs Asynchronous Execution

This is the conceptual root of everything else in this note.

### Synchronous (blocking)
- One request is handled **start to finish** before the worker can pick up the next one.
- If a request is waiting on I/O (DB query, external API call, disk read), the entire worker sits idle, doing nothing, blocking.
- Concurrency is achieved by running **multiple processes/threads** in parallel (e.g. Gunicorn spinning up N worker processes).
- Mental model: N cashiers, each fully occupied by one customer at a time, even while the customer is still digging for their wallet.

### Asynchronous (non-blocking)
- A single worker can hold **many requests in flight simultaneously** by switching to other work whenever the current task hits an I/O wait (via an **event loop**).
- Requires `async def` / `await` syntax in Python (built on [[asyncio]]).
- One process can juggle thousands of concurrent connections if most of the time is spent waiting on I/O rather than doing CPU work.
- Mental model: one skilled waiter taking orders from many tables — never standing idle, always switching to whoever's ready next.

### The critical nuance
Async doesn't make your code *faster* for CPU-bound work — it makes your code better at **not wasting time waiting**. If your workload is CPU-heavy (e.g. running inference, heavy tensor ops), async buys you very little and you actually want multiprocessing instead. Async shines when your bottleneck is I/O: network calls, DB queries, calling external LLM APIs, waiting on a vector store, etc.

> [!tip] For your RAG/inference work
> A RAG endpoint that calls an embedding API, then a vector DB, then an LLM API is *I/O-bound end to end* — an ideal case for async. But if you're running local model inference (GPU-bound compute) inside the request, async doesn't parallelize that computation; you'd want a task queue / worker pool ([[Celery]], [[Ray Serve]], batching) alongside your async endpoint instead.

Related: [[asyncio]], [[Event Loop]], [[Concurrency vs Parallelism]], [[GIL]]

---

## 3. WSGI — the synchronous standard

**Web Server Gateway Interface** (PEP 3333). The original, long-standing standard for how Python web servers talk to web frameworks.

- Defines a simple **synchronous** callable: `application(environ, start_response)`.
- One request → one function call → one response. No concept of holding a connection open or handling anything concurrently within that call.
- Cannot natively handle WebSockets, Server-Sent Events, or long-lived connections — it was designed in an era of simple request/response HTTP.
- Still extremely common and totally fine for CRUD apps, admin panels, most traditional web apps.

**WSGI servers:** Gunicorn, uWSGI, Waitress, mod_wsgi

**WSGI frameworks:** Flask (by default), Django (by default, pre-3.0 exclusively)

---

## 4. ASGI — the asynchronous standard

**Asynchronous Server Gateway Interface** — the spiritual successor to WSGI, designed to be a *superset* (it can still run sync code, but adds native async support).

- Defines an **async** callable: `async def application(scope, receive, send)`.
- Natively supports HTTP request/response **and** long-lived protocols: WebSockets, HTTP/2, Server-Sent Events.
- Lets a single worker process handle many concurrent connections via an event loop.
- Necessary if you want real-time features, streaming responses (e.g. streaming LLM tokens back to a client), or high-concurrency I/O-bound workloads.

**ASGI servers:** Uvicorn, Hypercorn, Daphne

**ASGI frameworks:** FastAPI (async-native), Starlette (the toolkit FastAPI is built on), Django (from 3.0+, supports ASGI too)

Related: [[WebSockets]], [[Server-Sent Events]], [[HTTP Streaming]]

---

## 5. Uvicorn, specifically

Uvicorn is an **ASGI server** — the piece of software that actually opens a socket, parses raw HTTP, and hands the parsed request to your ASGI-compliant framework.

- Built on `uvloop` (fast event loop, a Cython-based replacement for asyncio's default loop, itself built on libuv) and `httptools` for HTTP parsing — this is the source of its speed.
- Single-process/single-event-loop by design; production deployments run **multiple Uvicorn workers** for multi-core utilization.
- Common invocation:
  ```bash
  uvicorn main:app --reload          # dev
  uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4   # prod, native multi-worker
  ```
- Production pattern (traditional): Gunicorn as a **process manager**, spawning multiple Uvicorn workers:
  ```bash
  gunicorn main:app -k uvicorn.workers.UvicornWorker --workers 4
  ```
  Gunicorn here isn't doing the ASGI serving itself — it's supervising N Uvicorn processes (restarting crashed workers, handling graceful reloads), while each worker independently runs its own event loop.

Related: [[uvloop]], [[Gunicorn]]

---

## 6. Framework Landscape — who uses what

| Framework | Interface | Async-native? | Notes |
|---|---|---|---|
| **Flask** | WSGI | No (sync by default) | Can bolt on async views since Flask 2.0, but the underlying server is still WSGI — async views run but don't unlock true event-loop concurrency the way ASGI does |
| **FastAPI** | ASGI | Yes | Built on Starlette + Pydantic. Async-first but happily runs sync route functions too (dispatches them to a threadpool automatically) |
| **Starlette** | ASGI | Yes | Lightweight ASGI toolkit/framework. FastAPI is essentially Starlette + Pydantic validation + auto-generated OpenAPI docs |
| **Django** | WSGI (default) / ASGI (opt-in, 3.0+) | Partial | Historically WSGI-only; now supports ASGI deployment and async views, but large parts of the ORM are still sync (async ORM support has been landing incrementally) |
| **Tornado** | Neither (own protocol) | Yes | Predates ASGI; has its own async I/O loop and conventions, less common now that ASGI unified the ecosystem |
| **aiohttp** | Neither (own protocol) | Yes | Async framework *and* async HTTP client in one package; popular for both serving and making outbound async requests |

### The FastAPI stack, concretely
```
Your route functions (async def or def)
      ↓
FastAPI  (validation, DI, OpenAPI docs, routing sugar)
      ↓
Starlette  (the actual ASGI framework underneath)
      ↓
ASGI interface (the contract)
      ↓
Uvicorn  (the server process handling sockets/HTTP)
```

Related: [[FastAPI]], [[Flask]], [[Django]], [[Pydantic]]

---

## 7. Decision framework

| If you need... | Reach for |
|---|---|
| Simple CRUD app, no real-time features, team is more comfortable sync | Flask + Gunicorn |
| High-concurrency I/O-bound API (external API calls, DB-heavy, LLM calls) | FastAPI + Uvicorn |
| WebSockets / streaming responses (e.g. token-by-token LLM streaming) | FastAPI/Starlette + Uvicorn (ASGI is a hard requirement here) |
| Full-featured batteries-included app (admin panel, auth, ORM, forms) | Django (WSGI is fine unless you specifically need async views/channels) |
| Async framework + need to also make lots of outbound async HTTP calls | aiohttp |

> [!question] Rule of thumb
> If your endpoint spends most of its time **waiting** (on I/O) rather than **computing**, async + ASGI gives real concurrency wins. If your endpoint is CPU-bound (heavy local compute, e.g. running a model forward pass synchronously), async alone won't parallelize it — you need process-based parallelism or a dedicated inference server regardless of which web framework sits in front.

---

## 8. Open questions / things to explore further
- [ ] How does Django's async ORM support compare to fully async-native ORMs (e.g. SQLAlchemy 2.0 async, Tortoise ORM)?
- [ ] Benchmark Uvicorn vs Hypercorn for a streaming LLM response use case
- [ ] Look into how model-serving frameworks (Ray Serve, BentoML, TorchServe) sit relative to this stack — do they wrap Uvicorn/ASGI or bypass it entirely?

## Related Notes
[[FastAPI]] · [[Flask]] · [[Django]] · [[asyncio]] · [[Event Loop]] · [[REST API Design]] · [[Model Serving Infrastructure]] · [[RAG Pipeline Architecture]]



