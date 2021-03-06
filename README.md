# pqgrams - PQ-Gram Edit Distance

This is a fork originally from https://github.com/TylerGoeringer/PyGram and it has
adaptations from https://github.com/timtadh/PyGram/ and https://github.com/cathalgarvey/PyGram/

A Python implementation of the PQ-Gram algorithm for approximating tree edit
distance. For more information on the algorithm, please see the academic paper:
http://www.vldb2005.org/program/paper/wed/p301-augsten.pdf.

The PQ-Gram edit distance provides an extremely fast approximate tree edit
distance. In testing it appears to perform faster than any current approximation
for tree edit distance, with only a slight overhead in terms of memory usage.

The PQ-Gram edit distance is pseudo-metric:

1. Identity -- `edit_distance(a, a) = 0`

2. Symmetric -- `edit_distance(a, b) = edit_distance(b, a)` 

3. Triangle Inequality --
   `edit_distance(a, b) + edit_distance(b, c) >= edit_distance(a, c)`

In effect, this means that if the PQ-Gram distance between tree A and B is less
than the distance between tree C and D, then the true edit distance between A
and B is less than or equal to the distance between C and D. Note that an edit
distance of 0 does not mean the two trees are identical, only very similar.


## Usage

To use the PQGram.py distance you must complete three steps:

1. Re-write or modify tree.py. This class is currently a stub which provides a
   basic tree structure. In order for PyGram.py to function properly, this stub
   must be used, or a class with the same characteristics must be put in it's
   place. These characteristics include:

    - class Node()
    - def addkid(self, node, before=False)
    - self.label
    - self.children

    If the Node class does not have a label in the form of a string or children
    in the form of a list then the algorithm will fail.

2. Generate a PQ-Gram Profile. This can be done by simply creating an object of
   class Profile.

3. Call the edit\_distance method using two PQ-Gram Profiles.

    
In the original version there is no installation script, so one can simply add the source files to the
appropriate directory and use them.
Using this fork, the library can be installed via `pip install git+git://github.com/thdiaman/pqgrams.git`


## Tips

1. The p and q values will change the distribution of the edit\_distance
   function. This occurs for reasons that are more apparent if you read the
   paper.  The basic concept is that p controls the impact of ancestor nodes,
   and q controls the impact of sibling nodes. In practice, you will likely not
   need to modify these, as the preset values are reasonable. However, you may
   wish to tweak them to improve performance (either speed or accuracy) of your
   program.

2. When node labels are compared, it is using a binary string comparison. That
   is, the string "I am a dog" compared with the string "I am not a dog" results
   in a 0, whereas the string "Hello" and "Hello" results in a 1. This is due to
   limitations in the PQ-Gram Edit Distance algorithm. Whenever node labels are
   extended or overly descriptive, performance and accuracy of the algorithm can
   be increased dramatically by exploding node labels. This can be done using
   the helper method split\_tree. When given a tree, split\_tree will return a
   basic tree (that is to say, if you modify tree.py to include more than just
   the label value those values will be lost) which has each node split into a
   null node parent and child nodes based on a delimiter specified.

    *Example*

    Given the following tree

        A:B:C
          |
         A:B

    split_method(root, ":") would result in

              *
            / | \
           A  B  C
           |
           *
          / \
         A   B
         
    Note that the fact that A becomes the new parent is irrelevant because all nodes
    split using this method become left aligned. Given no delimiter, the function
    defaults to a full explosion, where each character in the label becomes it's own
    node. In practice, I have found this to be both the fastest and most accurate
    method, despite the dramatic increase in tree size.

    It is also important to note that split_tree is nondestructive. This ensures
    that if you modify tree.py so each node has more information than just a label,
    computing the split_tree for use in generating a PQ-Gram Profile would not
    destroy the extra data.

3. PQ-Gram always compares by the labels. Whatever other data you may have with
   the nodes, the edit distance comparison is always using just information in
   the labels. If you feel the data is necessary for proper comparison, you must
   include it in the label in some way. Once again, I highly recommend you use
   the split\_tree function in tree.py to ensure the correct level of
   granularity in the string comparison.

