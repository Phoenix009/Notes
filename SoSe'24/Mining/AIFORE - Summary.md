
**Core Insight**:
The structures and semantics of input fields are determined by the basic blocks that process them rather than the input specification.

**Approach Overview**:
1. Byte-level taint analysis to recognise the input bytes processed by each BB.
2. Identify indivisible input fields that are always processed together with a minimum cluster algorithm.
3. learn the types of fields with a neural network that characterises the behaviour of BBs.

# Introduction:

Sanity mechanisms in programs are not always sound and complete, it is highly possible that some inputs against the input specification are not filtered out but passed to *deeper* code. 

These inputs usually have a greater chance of triggering unexpected behaviour or bugs, and therefore should be favoured  by the fuzzer.

In general current solutions try to answer three core questions regarding the input format:
	1. where are the field boundaries?
	2. what are the types of the fields?
	3. how to use the knowledge of input format to guide fuzzing?

## Our Approach:

The core insight is that, since input fields are interpreted by BBs, the structural and semantic information of inputs can be inferred from them.

### 1. where are the field boundaries?
We assume that BBs can be used as a basis to identify field boundaries. 

We use dynamic taint analysis to learn which input bytes are processed by each BB.

Given the taint analysis we split field boundaries by recognising indivisible fields via a minimum cluster algorithm.

### 2. what are the types of the fields?
We build a deep learning model to learn the patterns of how BBs process different types of input fields and then predict the semantic type of input fields.

# Design

## Field Boundary Analysis

A field consisting of consecutive bytes is usually processes as a whole in each BB.

1. split the binary code into BBs
2. collect and merge the bytes processed in one BB
3. split fields based on each blocks taint attributes

The MC methods can find most input fields correctly. however, there are some exceptions.

## Field Type Classification:

The program code processing fields of different types show different patters.

Based on this observation, we propose to classify the pattern of BBs to infer the types of input fields processed by them.

We train a CNN model to predict the field type from a target code snippet.

### Field Types:
We consider semantic types rather than their program variable type. Such semantic types are more challenging to identify and are more beneficial for fuzzing.

We support six semantic types which can help the fuzzer mutate the field more efficiently for most real world programs.

- Size
- Enumeration
- Magic Number
- Strings
- Checksum
- Offset

### Data Vectorisation:
Several features are considered when vectorising code slices.

Semantic information of instructions in BB is important. Thus we convert the BB into its IR and store the important IR instructions in a one hot encoded vector

We also want to store the stdlib function calls made in the BB. Also stored as a one hot encoded vector

The format strings used in BB can also be helpful in determining the semantic information of the input field getting processed. This again is stored as a one hot encoded vector.

All the one hot encoded vectors of IR, stdlib function and format strings are concatenated together into a single feature vector.

### CNN Model Building:
We choose the CNN model to infer the field type for two reasons. 
1. CNN models are proven effective in many classification tasks and binary analysis tasks. 
2. CNN models can naturally filter out the background information and effectively capture valuable features.

## Fuzzing with Power Scheduling
We have the following two problems when using the knowledge to help fuzz:
1. on which seed should we perform format extraction?
2. how can we balance the fuzzing power for different format variants produced during mutation


The fuzzer generates numerous seeds during fuzzing. Several works have proved that not all seeds are valuable during fuzzing. So we extract the format knowledge only for the initial seed and the seeds which we consider valuable.

During mutation, whenever a valuable seed occurs, the fuzzer starts mutating the seed based on the re-analysed format. When the fuzzer gets stuck, we re-assign the fuzzing power to those seeds bound with less mutated format variants and skip those seeds bound with the fully mutated format variants.


