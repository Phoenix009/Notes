# Introduction:

Feedback directed fuzzing tools such as AFL and libFuzzer are format-agnostic meaning that they assume no knowledge about the input format when producing or mutating inputs. Thus are easy to set up and deploy.

There are limitations:
1. Sample input files to be mutated are available
2. Instrumentation and feedback from the program under test is available
3. **cost of producing invalid inputs is bearable**

Creating a format specific fuzzer comes with a considerable effort when one wants to reuse the guidance and analysis capabilities of feedback directed fuzzers

Combines the flexibility of format agnostic fuzzers with the efficacy of format specific fuzzers.

Format fuzzer framework leverages existing binary specifications of input formats to produce or mutate valid file inputs, even in black box settings where no guidance from the program is available; and integrate with arbitrary format agnostic fuzzer to make them format aware.

010 editor allows users to view and edit binary files with detailed structural information. It parses its inputs using human written input specification files known as binary templates.

For many popular fuzzing targets, a binary template which describes its input format is already available online.

Of special interest are multimedia files formats, which often come from untrusted sources and therefore present a large attack surface, with potentially serious security consequences.

Some of these binary templates also contain context sensitive information which is a major roadblock for format agnostic fuzzers.

FormatFuzzer takes as input these binary templates and compiles it into
1. a highly efficient *fuzzer* that fuzzes outputs in format specified
2. A *parser* that reads existing inputs in the format specified.

Nevertheless, some manual effort is still required. The existing binary templates work for parsing but to make effective fuzzers we have to refine them further.

Contribution of the paper:
1. a novel description format for generating binary inputs.
2. Decision seeds to synchronise generators and parsers.
	- decision seeds are a sequence of bytes reflecting the decisions taken during generating or parsing.
	- After parsing the input, the resulting decision seed allows generator to retake the same decisions which reproduces the input exactly
	- This allows to apply smart mutations over decision seeds which are obtained from a high quality corpus of binary files.
3. Making format agnostic fuzzers input format aware.
	- If a format agnostic fuzzer is set to mutate and evolve decision seeds, the format fuzzer can then translate any such mutation into a valid binary input.
	- Provides smart mutations which operate over decision seeds. Enables approach to improve any format agnostic fuzzer, by leveraging format specific mutations provided by format fuzzer. 


# Binary Template Language:
The binary template language was designed to fully specify any binary format. The choice of binary template language as the base for FormatFuzzer is purely pragmatic.

Whether all subtleties of input formats can be specified in a more elegant or useful way is yet to be proven.

## Binary Templates for Generation:

A value will have to be chosen for every variable that is declared and also for every lookahead function that is called.

We extend the binary template language in a number of apsects:
1. Choices of valid values: 
2. Enumerations: 
3. Lookaheads:
4. Allowing invalid choices: Allow "evil" decisions to be made during the generation. Enables greater diversity of generated files by exploring invalid choices
### Magic Values:
magic values are bytes in the file that need to have a specific value. FormatFuzzer is able to automatically mine such values when analysing the source code of the binary template. (What if there is no white box access?)

### Size Fields:
Another common feature is the presence of size fields, whose value corresponds to the size of a certain structure in the file. 

One challenge is that the length field can appear before the data it refers to. The strategy we use is to use the initial value sample for length as only a hint. Once the data is generated this sample value can be wrong. If the size value is wrong then we go back and overwrite it.

### Complex Constraints:
- one common pattern is when value of one variable restricts the possible value of some other variable.
- Handled by using a lookahead function in parsing.
- In generation handled by using a fix value for a lookahead.
- Checksums are handled simply by computing the checksum and specifying it as the desired value for the checksum variable.

### Chunk Ordering:
- The set of possible types for the next chunk is usually context sensitive. 
- Parsing is handled easily but while generating we ned to specify the set of possible choices and allow those choices to change for each new generated chunk.
- We solve this by specifying three new arguments to the lookahead function.
	1. preferred: specifies the next mandatory chunk
	2. possible: set of chunks possible at current position
	3. probability: to pick from the preferred values
- If the preferred array is empty then the generator can either choose to generate an optional chunk from the possible array or decide to stop the generation.


# Decision Seeds:
To generate a file, we first provide an array of random bytes called the decision seeds. This array is the source of randomness for generator decisions.
Such decision seeds can also be reconstructed by Format Fuzzer while parsing.

## Decision seeds for generation:
The generation process using decision seeds is completely deterministic. The decision seed encodes essential features of a given file in a representation that is tailored for fuzzing.

The correspondence between the decision seeds and the generated files can be thought of as a partial function $f: \text{seed} \nrightarrow \text{file}$ 


This mapping is not injective, but ideally every valid file should be obtainable by the generator i.e.: $\text{Valid} \subseteq f(\text{Seeds})$


## Decision trees during parsing:
While parsing a decision tree can be obtained by reconstructing at each step the decision bytes that would be required to produce the target file contents.

One important axion of our framework is that a file can be successfully parsed by FormatFuzzer iff the file can be generated from some appropriate decision seed.


# Fuzzing Strategies:

## Mutating Inputs:
### Smart Abstraction:
Allows abstracting one specific chunk $c_1$ into a new random version $c_2$ . Works by first parsing the original file and obtaining its decision seed. Then, we randomise the specific decision byte for our chunk $c_1$ and generate the new file.

### Smart Replacement:
Replaces a chunk $c_1$ from file $\text{file}_1$ with a different chunk $c_2$ of the same type from file $\text{file}_2$. We get the decision seeds for each file and replace the decision byte of our chunks. This mutated decision seed is then used by the generator to produce the mutated file.

### Smart Deletions:
Smart delete operation deletes a chunk from a file by removing the decision bytes which were responsible for the generation of the chunk. 

Such an operation only makes sense when the target chunk is an optional chunk.

We allow smart deletion to be performed over a chunk $c$ only when a lookahead function is called before and after the generation of $c$.

### Smart Insertions:
Smart insert is an inverse operation of the smart delete operation. The inserted chunk $c$ should also be optional. 


## Integrating with Format Agnostic Fuzzers:
- One way is to use AFL for mutating decision seeds and then use the mutated seeds to generate mutated files.
- AFL uses the coverage feedback from the program to learn how to effectively mutate the decision seeds.
- Another way is to add smart mutations to the mutation operations of the AFL.

