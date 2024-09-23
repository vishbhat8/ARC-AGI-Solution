# ARC-AGI Solution (Rule-based)

The following is my rule-based solution for the ARC-AGI benchmark. It has been tested on the first 200 examples from the evaluation set and it is able to address and solve all of them. I am slowly adding worked-out solutions for each problem from the eval set into this repo (check [solutions/evaluation directory](https://github.com/vishbhat8/ARC-AGI-Solution/tree/main/solutions/evaluation)).

This repo only contains the architecture and the approach, not the code. In order to help implement the architecture described below, I am putting together a team. DM if you are interested in helping us. All the code for this project will be open-source.

**Note:** I am not claiming that the architecture described below can solve problems based on general intelligence in any way. This architecture is great at solving problems based on the criteria defined in the following paper: https://arxiv.org/pdf/1911.01547

## Architecture

The problems from ARC-AGI can be classified into 2 types, based on my understanding of the official publication and the problem set:

1. Fill-in-the-blanks type problems usually involving symmetry
1. Goal-directedness problems

Most problems of the first type can be solved by taking the mirror images of the input along either x or y or diagonal axes, so I will not be addressing them immediately. We can come back to them later.

Problems of the second type are more complicated. I have come up with the following steps to solve such problems:

### Step 0: Score of a state
Definition: In the given task examples, the score of a state is used to measure how close the given state is to the output. Here is the procedure:
1. If a coordinate in a state's grid is filled and the same coordinate in the output's grid is also filled, add 0.5 points.
1. If a coordinate in a state's grid is filled with the same color as that in the output's grid, add another 0.5 points.
1. For everything else, no points given
1. Score calculation is invalid if the grid sizes of the given state and that of the output don't match


### Step 1: Get all observations
Definition: All possible observations from the examples

#### 1.1: Objects
Definition: All possible objects that can be extracted from the given examples, both inputs and outputs.

##### 1.1.1: Continuous-color
Definition: Shapes that are continuous and are of the same color

##### 1.1.2: Continuous
Definition: Shapes that are continuous and are not of the same color

##### 1.1.3: Regular
Definition: Regular shapes of the same color for this benchmark are the following: rectangle, square, cross (plus shape), circles, and diagonal lines.

##### 1.1.4: Overlapping regulars
Definition: Regular shapes of the same color that are overlapping

#### 1.2: Object params
Definition: Store the values for all the object params. The following are the object params:

- colors: int array -> all the colors in the obj
- color: int -> valid only if 1 color present in the obj
- size: int
- grid-size: int arr -> 2D coordinates
- type: string -> type of object
- coordinates: arr of coordinates
- shape: string -> valid only if a regular obj
- x1 (smallest x-coordinate in the obj): int
- x2 (largest x-coordinate in the obj): int
- y1 (smallest y-coordinate in the obj): int
- y2 (largest y-coordinate in the obj): int
- placement: arr of coordinates -> [(x1 + (x2-x1)//2, y1 + (y2-y1)//2)]
- centroid: arr of coordinates -> [(x1 + (x2-x1)//2, y1 + (y2-y1)//2)]
- cavities: arrays of coordinates of the cavities of the object
- bottom-left coordinate: coordinates of the bottom-left pixel of the image, with preference to x-axis
- top-right coordinate: coordinates of the lowest bottom pixel of the image, with preference to x-axis
- ... more being added (from my notes)

#### 1.3: Self-relations
Definition: All possible self-relations between the object parameters. The following are the types of self-relations, which records all instances of:

- an object/color being above or below another
- an object/color being to the left or to the right of another
- an object/color being contained by another
- an object/color being in contact with another
- an object having the same pattern/color/placement/size/shape as another
- an object having the same pattern/color/placement/size/shape as the cavity of another
- an object having the same pattern/color/placement/size/shape as the perimeter of another
- an object having the same pattern/color/placement/size/shape as content (everything in the object except the perimeter) of another
- ... more being added (from my notes)

### Step 2: Changes
Definition: List of all the changes from the input to output in the given example. This includes a list of which objects were retained from the input, which were removed, and which output objects were added.

### Step 3: Dict of all possible changes to observations (if-statements)
Definition: Mapping of all possible changes to observations. Here, we try to explain each change in terms of the params and self-relations.

- Format of each if-statement: `<literal1> if <literal1> AND <literal2> AND ... <literalN>`
- An if-statement is valid only if the score gain is > 0.

### Step 4: Minimal number of if-statements
Definition: Intersection of the common if-statements and union of other required if-statements such that we have the minimal number of if-statements needed to get to the output in each case.

### Step 5: Apply to the test case
Definition: Apply the if-statements to the test input and get the output


**Note**: For more clarification on the steps and to get a better understanding of the architecture, please review the worked-out solutions in the [solutions/evaluation directory](https://github.com/vishbhat8/ARC-AGI-Solution/tree/main/solutions/evaluation). Please DM me if you have any thoughts to share. 
