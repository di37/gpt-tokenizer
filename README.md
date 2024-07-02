# Let's Build GPT Tokenizer - From Scratch

## Why is Tokenization Necessary?

LLMs can't directly process raw text. They require a way to convert text into numerical representations that can be fed into their neural network architecture. This is where tokenization comes into play.

- **Bridging Text and Models:** Tokenization acts as a bridge between human-readable text and the numerical world of LLMs.
- **Numerical Representations:** It breaks down text into smaller units called "tokens," which are then mapped to integers. These integers are used as lookups into a table of vectors, which represent the tokens in a numerical form understandable by the LLM.

## Naive Tokenization: Character Level

A simple approach to tokenization is character-level tokenization, where every character in the text becomes a token.

**Example:** In the "Let's build GPT from scratch" video, character-level tokenization was used.

1. **Building a Vocabulary:** A vocabulary was created, containing all unique characters from the Shakespeare dataset used.
2. **Creating a Lookup Table:** A lookup table was created to map every character to a corresponding integer token.
3. **Encoding the Text:** Using this lookup table, the text was converted into a sequence of integer tokens, one for each character.
4. **Embedding the Tokens:** These integer tokens were used to retrieve corresponding vectors from an embedding table. This embedding table provides a numerical representation of each token for the LLM.

## State-of-the-Art Tokenization: Byte Pair Encoding (BPE)

Modern LLMs use more sophisticated tokenization schemes, like Byte Pair Encoding (BPE).

- **Character Chunks:** Instead of individual characters, BPE tokenizes text into character chunks.
- **Variable Length Tokens:** These chunks can be of variable length, enabling more efficient encoding.

## Why not use raw Unicode code points for tokenization?

While Unicode code points represent characters as integers, directly using them for tokenization presents some challenges:

- **Large Vocabulary:** Unicode has a vast vocabulary size (~150,000 characters), resulting in a massive embedding table and potentially slow processing.
- **Ever-evolving Standard:** The Unicode standard constantly changes, which could lead to inconsistencies and instability in the tokenization process.

## Encoding Unicode Text: UTF-8

To handle Unicode text efficiently, encodings are used to translate it into binary data. UTF-8 is the most common encoding for Unicode text.

- **Variable Length Byte Stream:** UTF-8 encodes every Unicode code point into a byte stream, varying in length from 1 to 4 bytes.

**Other Encodings:**

- **UTF-16:** Similar to UTF-8, but uses 2 or 4 bytes per code point.
- **UTF-32:** Uses a fixed length of 4 bytes per code point, making it less efficient for common characters.

**Why UTF-8?**

- **Commonly Used:** UTF-8 is the most widely used encoding on the internet.
- **Backward Compatibility:** It is backward compatible with ASCII, making it more versatile.

## Using UTF-8 for Tokenization

While using raw bytes of UTF-8 encoding would imply a limited vocabulary size (256), leading to long sequences and inefficient context usage, BPE provides a solution.

- **Compressing Byte Sequences:** BPE allows compressing these byte sequences while using the efficient UTF-8 encoding.

### How BPE Works

BPE iteratively merges pairs of bytes in a sequence based on their frequency, creating a compressed representation.

1. **Identify most frequent pair:** Analyze the sequence and identify the most frequently occurring pair of bytes.
2. **Mint a new token:** Create a new token to represent this pair and add it to the vocabulary.
3. **Replace occurrences:** Replace all instances of the frequent pair with the new token.
4. **Repeat:** Repeat steps 1-3 until reaching a desired vocabulary size.

This process allows for a more compact representation of the text while maintaining a manageable vocabulary size.

## Important Considerations with BPE Tokenization

- **Final Vocabulary Size:** The final vocabulary size is a hyperparameter that needs to be chosen based on the desired tradeoff between sequence length and computational cost.
- **Separate Object:** The tokenizer is a distinct object from the language model, with its own training dataset and preprocessing steps.
- **Training Dataset for Tokenizer:** The choice of training data for the tokenizer significantly impacts the density and efficiency of the final token representation. It should ideally contain a mix of languages and code relevant to the intended use case of the LLM.

## Complexities and Issues Associated with Tokenization

Tokenization can be at the heart of several complexities and strange behaviors observed in LLMs.

- **Spelling Tasks:** LLMs may struggle with spelling tasks, often due to character chunks being combined into larger tokens, making it difficult for the model to understand individual letters within a token.
- **String Processing:** Simple string operations like reversing strings can be challenging for LLMs because of the way tokens represent chunks of text rather than individual characters.
- **Non-English Languages:** Performance on non-English languages can be significantly worse because tokenizers are often trained on predominantly English datasets. This leads to shorter, more dense tokens for English and longer, more fragmented tokens for other languages, resulting in inefficient context usage for the LLM.
- **Arithmetic:** LLMs can be surprisingly bad at arithmetic, often due to the arbitrary way digits are tokenized. Depending on the merging process, numbers can be represented as single tokens, multiple tokens, or a combination of both, making it challenging for the model to consistently understand and manipulate numerical values.
- **Python Code:** GPT-2 was notoriously bad at handling Python code. This was largely attributed to the inefficient tokenization of spaces, which significantly bloated the sequence length, exceeding the limited context length of the model.
- **Trailing Whitespace:** Seemingly minor issues like trailing whitespace can become problematic due to how tokenizers handle these characters.
- **Trigger Words:** Certain seemingly benign words can trigger unpredictable behavior in LLMs. This often stems from inconsistencies in the training datasets for the tokenizer and the language model itself, resulting in "untrained" tokens that evoke undefined behavior when encountered.

## Notes

These notes are created using Gemini-1.5-Pro available on ai.google.dev. 