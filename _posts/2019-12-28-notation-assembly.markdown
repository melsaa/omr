---
layout: post
title:  "Notation Assembly"
date:   2019-12-28 14:18:09 +0000
---

## Definition
Notation Assembly is the process of recovering the music notation semantics from the detected and classified symbols. The output is a symbolic representation of the symbols and their relationships, typically as a graph.

## Current Approaches
Due to the complexity of how musical semantics are inferred from the image, it is difficult (for now) to formulate it as a learnable optimization problem. While end-to-end systems for OMR do exist, they are still limited to a subset of music notation, at best. Some recent works have considered deep recurrent neural networks for monophonic music written in both typeset and handwritten modern notation.

### Using A Grammar for A Reliable Full Score Recognition System (1995)
Propose a grammar to formalize the musical knowledge on full scores with **polyphonic** staves.
The objects on a score can be divided into 2 categories:
1. Constructs: composed of **segments** (stems, beams) and notehead
2. Symbolic: clefs, accidentals, etc. These can be considered as characters and recognized by using OCR

# Graphical Part
Example: rules for `beamedNote` <br>
<img src="https://i.imgur.com/OMviEnn.jpg" alt='beamedNote' width='450' height='300'>

# Syntactic Part
Introduce the notion of Step, which corresponds to the smallest duration in a column of vertically aligned notes. It can manage the simultaneity (i.e. the verticality) of notes on the same staff (in case of polyphony), and on the full scores. Informations associated to the staff (i.e. dynamics, slurs) are introduced in the grammar at Step level. <br>
<img src="https://imgur.com/OUm7j1a.jpg" alt='syntactic' width='400' height='300'>

# Staves System
Assign each musical symbol to the right bar
<img src="https://i.imgur.com/vbvG61W.jpg" alt='staffMap' width='400' height='300'>

### A Starting Point for Handwritten Music Recognition (2018)
* Input: handwritten music scores annotated at symbol level (need to re-label the dataset)
* Output: binary matrix corresponds to rhythm (80 classes) and pitch(28 classes)
* Rhythm: 4, 3, 2, 1, 0.5, ..., dynamics, slurs, rests, sharp
* Pitch: C1, D1, ...
* Pipeline: Input -> Conv_block -> Bi-LSTM -> Dense_Layers -> Output
* Staves are cropped and processed independently, resized to 100px height
* Use Convolutional Neural Network to extract meaningful features for each staves
* Use Bidirectional LSTM to recover the musical semantics
* Chords are translated into sequences: [Start Chord], [quarterNote, Line1], [quarterNote, Space2], [quarterNote, Line4], [End Chord]
* Transfer learning from typeset to handwritten musical notation
* Evaluation metric: Symbol Error Rate (SER) similar to Word Error Rate (WER)

### Towards Full-Pipeline Handwritten OMR with Musical Symbol Detection by U-Nets (2018)
Pipeline: input -> symbol detection (U-Net) -> recover graph edges -> recover precedence edges -> convert to MIDI file

# Symbol Detection
* Propose Convex-Hull segmentation targets, works good to detect f-clefs and c-clefs <br>
<img src="https://imgur.com/mDNMDR7.jpg" alt='convex' width='400' height='200'>
* Using U-Net is viable, but is limited by the size of receptive field: the middle of a long stem looks exactly the same as a barline
* Further research: leverage syntactic properties of music notation: self-attention layer that allows building up the final output from partial recognition results

# Notation Assembly and Pitch Inference
* MUSIMA++ semantics are represented as an oriented graph, once the graph is recovered, one can perform deterministic pitch inference
* Symbol detection outputs vertices of the notation graph; the next step therefore is to recover graph edges
* Method: same as [here][muscima], train a binary classifier over ordered symbol pairs
* Drawback: make embarrassing erros, i.e. connect noteheads with irrelevant symbols (multiple adjacent stems, beams from different staff)
* Further research: combine with grammar rules to reduce errors

## Reference:
1. [Understanding Optical Music Recognition][omr-exp]
2. [Using A Grammar for A Reliable Full Score Recognition System][grammar]
3. [A Starting Point for Handwritten Music Recognition][start]
4. [Towards Full-Pipeline Handwritten OMR with Musical Symbol Detection by U-Nets][u-net]
5. [An End-to-End Trainable Neural Network for Image-based Sequence Recognition and Its Application to Scene Text Recognition][scene]
6. [End-to-End Neural Optical Music Recognition of Monophonic Scores][monophonic]

[omr-exp]: https://arxiv.org/pdf/1908.03608.pdf
[grammar]: http://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=F44590BADB2A8997A735F3E5F4391416?doi=10.1.1.29.4927&rep=rep1&type=pdf
[start]: https://openreview.net/pdf?id=SygqKLQrXQ
[u-net]: https://pdfs.semanticscholar.org/f05c/b3674df33d35e562ca79b9b3af2e10c1a88a.pdf
[scene]: https://arxiv.org/pdf/1507.05717.pdf
[monophonic]: https://www.mdpi.com/2076-3417/8/4/606
