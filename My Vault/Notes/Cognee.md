
# Cognee


DATE:  21-10-26


Tags: [[AI Memory]]


# References:




# Content:

![[Attachments/Pasted image 20261021135954.png]]
![[Attachments/Pasted image 20261022023537.png]]


- Cognee sits between Data and Agents. Effectively creates a data map from the datasources which then constantly keeps updating with each agent session
- Takes care of deduplication, time related issues 
- ![[Attachments/Pasted image 20261021141227.png]]
- The datamap is built and stored in Postgres, effectively 3 databases gets built.

- The datamap is actually custom Pydantic models created from datasources, that can be then transformed into graph in any supported graph/vector store providers(over 15 supported). And it takes care of the underlying apis for the transformation. 
- Auto ontology builder

- Can retrieve all kinds of data with all kinds of strategies (16 retriever types). Based on the type of questions eg. temporal, semantic, it need to be answered by different types of combinations of search strategies and it smartly route through those and give the best answer needed.
- ![[Attachments/Pasted image 20261021143854.png]]
- ![[Attachments/Pasted image 20261021144044.png]]
- Do not copy the data over, copy only the structure so agents can navigate the data systematically

- It mainly acts as a context manager. For example, in Claude Code, it can be added via plugins. It uses hooks to intercept and load the data inside of the memory. Every time we interact with the system whether it's running the agents, sub agents or workflows, it constantly asks Cognee about the relevant context and the relevant information. So once we have loaded the data and connected Claude code to Cognee, it always going to use the right context to answer the question. This context can be code, mail, Teams messages.

- ![[Attachments/Pasted image 20261021153322.png]]
- Small model to make the memory free
- Many transformation modules available like dedup, PII modules ets
- If we don't have ontologies preset, it can autodetermine them and autopopulate them as a part of the paid services. 
- Personal context vs Org context. We should not push all the knowledge to org context. We should have a personal context first and then have ability to propagate memory to what the org needs to know and the vice versa. This Propagation system is part of paid services.
- Evolving ontology is also not part of the open source

- ![[Attachments/Pasted image 20261021184006.png]]