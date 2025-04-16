
# Asynchronous Programming


DATE:  11-04-25


Tags: [[Notes/python|python]]

# References:
https://www.youtube.com/watch?v=t5Bo1Je9EmE


# Content:

**Synchronous** - 
- Everything is happening sequentially
- ![[Attachments/Pasted image 20250411174210.png]]`foo()` has to complete executing before running `print('tim')`
- The next operation waits for the current one to complete. Performance is often tied to processor clock speed. Fast processor -> Fast runs


**Asynchronous Programming** - 
- Execution of code in a non-blocking manner. While one task is waiting for an I/O-bound operation (like network requests or file operations), the program can execute other code.
- **Co-routine:** A special type of function defined with the async keyword. It is a wrapped version of theCalling a co-routine does not directly execute its code but returns a co-routine object.
- **async Keyword:** Used to define a co-routine function.
- **await Keyword:** Used inside an async function to pause the execution of the current co-routine and wait for another awaitable object (like another co-routine, a future, or a task) to complete. This allows the event loop to run other tasks.
- **Event Loop:** The core of asyncio. It manages and schedules the execution of co-routines and handles I/O operations. You typically start the event loop using asyncio.run().
- **Task:** Represents a concurrently running co-routine. You can create a task using asyncio.create_task() and it gets scheduled to run on the event loop.
- **Future:** An object that represents the result of an asynchronous operation that may or may not be complete yet. Tasks are a type of future. It acts as a placeholder for a value that will be available in the future.

def plot_top_disagreements(self, model_num=2, top_n=5): """ Create a heatmap showing the top disagreement pairs Parameters: model_num (int): Which model to analyze (1 or 2) top_n (int): Number of top disagreement pairs to show Returns: matplotlib.figure.Figure: The generated plot """ model_col = f'new_model_{model_num}' # Get counts of current intent vs new model intent cross_counts = pd.crosstab(self.df['current_model'], self.df[model_col]) # Zero out the diagonal (we're only interested in disagreements) for intent in cross_counts.index: if intent in cross_counts.columns: cross_counts.loc[intent, intent] = 0 # Reshape to get pairs melted = cross_counts.reset_index().melt(id_vars='current_model', var_name='new_prediction', value_name='count') melted = melted[melted['count'] > 0].sort_values('count', ascending=False) # Get top N disagreement pairs top_pairs = melted.head(top_n) # Create a heatmap of just these pairs plt.figure(figsize=(12, 8)) # Create custom matrices for the heatmap from_intents = top_pairs['current_model'].unique() to_intents = top_pairs['new_prediction'].unique() all_intents = sorted(list(set(from_intents) | set(to_intents))) # Create matrix matrix = np.zeros((len(all_intents), len(all_intents))) intent_indices = {intent: i for i, intent in enumerate(all_intents)} for _, row in top_pairs.iterrows(): i = intent_indices[row['current_model']] j = intent_indices[row['new_prediction']] matrix[i, j] = row['count'] # Plot heatmap sns.heatmap(matrix, annot=True, fmt='g', cmap='YlOrRd', xticklabels=all_intents, yticklabels=all_intents) plt.title(f'Top {top_n} Disagreement Pairs: Current Model → Model {model_num}') plt.xlabel('Model {model_num} Prediction') plt.ylabel('Current Model Prediction') plt.xticks(rotation=45, ha='right') plt.tight_layout() plt.savefig(f'top_disagreements_model{model_num}.png', dpi=300) return plt.gcf()

