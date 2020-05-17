Timothy Samson

# (1) Error-correcting codes

With 3 illustrations

## (1.1) Introduction: Intergalactic Confusion

Suppose we are a satellite which needs to send messages to Earth. Any binary message which we send to Earth may be distorted at random locations. How can we send messages so we can correct for these random errors? If we send our data naively -- that is, sending just our message -- it is impossible to determine that an arbitrary bit is correctly sent or not. However, if we append extra bits to our message, we can correct for errors in our message reliably. 

An error-correcting code can be defined as a **procedure** for creating an encoded message $EM$ from a message $M$, where $EM$ is to be sent to Earth *over a channel with interference*, so that when Earth receives $EM'$, Earth has a way to **decode** $EM'$ to form the original message $M$', where $M \approx M'$. 

Here are two approaches to creating an error correcting code.

## (1.2) Repetition code

Suppose we want send Earth "H," which can be encoded in binary as "0100" (This is only half of the binary for the ASCII encoding of "H" to save space)

The repetition error-correcting code will  repeat each bit three times. When the encoded message is received by Earth, perform a "best two-out-of-three" vote for each chunk to determine if the chunk is interpreted as a zero or a one. So, if Earth receives a "$010$" chunk, Earth will interpret it as a $0$ 

The procedure is outlined as follows

| $M$                                    | $0100$                                      |
| -------------------------------------- | ------------------------------------------- |
| $EM$                                   | 000 111 000 000                             |
| $EM'$ (after being sent through space) | 0<u>1</u>0 <u>0</u>11 000 <u>1</u>0<u>1</u> |
| $M'$ (After decoding $EM$)             | 010<u>1</u>                                 |

The procedure will not catch errors 100% of the time, especially under mediums with heavy interference. if two errors happen in a single chunk, then the repetition code will fail.

### (1.2.2) Efficiency and accuracy of the repetition code

Let us define two measures to compare our error-correcting codes. One measure is called ***efficiency***, which is a measure of how much space an error-correcting code saves, while another is ***accuracy***, or how resilient our error-correcting codes are under harsh interference mediums.

The problem with this approach is that the message size gets tripled. So, the efficiency is 
$$
\frac{|M|}{|EM|} = \frac{1}{3} \approx 33\%
$$
Its accuracy is a measure of how many errors it can capture per bit in the $EM$. It can capture $1$ error per $3$ bit chunk, so its accuracy is $\frac{1}{3} \approx 33\%$. Therefore, this method is optimal when sending into a medium where around $33\%$ or less of the bits are flipped randomly.

There are other, more efficient methods.

## (1.3) Hamming (7, 4) code

This is a more efficient code, although less accurate as the naive method, with an efficiency $\frac{|M|}{|EM|} = \frac{4}{7} \approx 57\%$. The procedures goes as follows.

Suppose we want to send the message "Hi" which is "$01001000$  $01101001$" in binary. It will be convenient to divide the message in four-bit chunks: "$0100$  $1000$  $0110$  $1001$"

To compute $EM$,  label the 4 bits in each chunk "$abcd$," which we'll call the *message chunk*. Then, append  "$xyz$" to each four bit chunk, which we call the *check chunk*, where 
$$
x = b \oplus c \oplus d \\
y = a \oplus c \oplus d \\ 
z = a \oplus b \oplus d
$$
So, for example, our first four-bit chunk $0100$  will produce $xyz$ equal to $101$, so the 4-bit chunk $0100$ in $M$ will be converted into the 7-bit chunk $0100101$ in $EM$ 

Suppose after being sent through space, the third bit (the $c$ bit) in $EM$ flips: $01\underline{1}0101$ in $EM'$. Then we are in luck, since the Hamming (7,4) code can correct for errors *which flip only a single bit* in our 7-bit chunk (If both the first and the third bit flips $\underline{1}1\underline{1}0101$ then Hamming will produce unexpected behavior).  

Computing the check chunk for $0110$ will yield $011$ which is different from the received check chunk $101$, so Earth would know something is wrong in the message. Earth observes that the $x$ and $y$ values are incorrect, and the $z$ value is correct. Looking at the formulas for $x$ and $y$
$$
x = b \oplus c \oplus d \\y = a \oplus c \oplus d
$$
Earth notices that $c$ and $d$ are repeated, so flipping either $c$ or $d$ will yield an incorrect value for both $x$ and $y$. So, $c$ or $d$ are likely to be the incorrect bit. Further investigations to eliminate the candidates will note that $z = a \oplus b \oplus d$, so if $d$ was the incorrect bit, then $z$ would be incorrect. But our computed $z$ matches with the received $z$. Therefore, $c$ is the incorrect bit. 

To summarize the procedure to correct the bits, we define a 3-bit code which codifies the difference between the received check chunk and the computed check chunk in $EM'$. For example, $101$ signifies that the $x$​ and the $z$ bit differ from the computed and the received check chunk, while $000$ means that there are no errors.

For a $EM'$ chunk $abcd xyz$

| Error code | Action                                    | Error code | Action       |
| ---------- | ----------------------------------------- | ---------- | ------------ |
| 000        | Nothing wrong                             | 011        | flip $a$ bit |
| 001        | $z$ bit wrong (leave message chunk as is) | 101        | flip $b$ bit |
| 010        | $y$ bit wrong (leave as is)               | 110        | flip $c$ bit |
| 100        | $x$ bit wrong (leave as is)               | 111        | flip $d$ bit |

If only one of the check bits are incorrect, then we assume only the check bit is the bit that was flipped through space.

The entire procedure is outlined as follows

| $M$                              | $0100$  $1000$  $0110$  $1001$                               |
| -------------------------------- | ------------------------------------------------------------ |
| $EM$                             | $0100 101\ \ 1000 011\ \ 0110 011 \ \ 1001 100$              |
| $EM'$                            | $0100 \underline{0}01\ \ 1\underline{1}00 011\ \ \underline{1}1\underline{0}0 011 \ \ 100\underline{0} 100$ |
| Check bits *obtained* from $EM'$ | $001\ \ 011\ \ 011\ \ 100$                                   |
| Check bits *computed* in $EM'$   | $\underline101\ \ \underline11\underline0\ \ \underline11\underline0\ \ \underline{011}$ |
| Error code                       | $100 \ \ 101\ \ 101\ \ 111$                                  |
| $M'$                             | $0100\ \ 1\underline{0}00 \ \ 1\underline{0}00\ \ 100\underline1$ |

### (1.3.2) Efficiency and accuracy of the Hamming (7,4) code

For each 4-bit chunk we append a 3-bit check chunk, so the efficiency is 
$$
\frac{|M|}{|EM|} = \frac{4}{7} \approx 57\%
$$
which is more efficient than the naive method. It can correct one bit out of every 7-bit chunk, so the accuracy is $\frac{1}{7} \approx 14.2\%$. So the Hamming (7, 4) code is optimal for channels with random interference of $14.2\%$ or less. 

[2]

<div style="page-break-after: always;"></div>

# (2) Hamming's Brilliant Idea

## (2.1) Hamming Distance 

***Hamming distance*** is defined as the amount of bits to flip for one sequence of bits to equal another. For example, The Hamming distance between $10001$ and $10111$ is $2$, because the third and the fourth bit has to be flipped for one to equal another

We define a ***codeword*** as simply a sequence of $n$ bits inside a specific set. It is the set of "words" (bit sequences) which we give meaning to, like the set of all words in a dictionary. If Earth receives a message that is not a codeword, then Earth will interpret it as a codeword which has the *least distance* from the invalid word.

**Theorem:** According to Hamming, given a set of codewords with a minimum Hamming distance *of each pair* as $d = 2e+1$, then one can correct up to $e$ errors.  

This statement can be illustrated by an example. Suppose we have the set of three codewords
$$
11010 \\
01101 \\
10001
$$
Notice that the minimum distance of each pair of codewords is $3 = 2\cdot1 + 1$. Any messages sent is composed of combinations of these three codewords. Therefore, according to Hamming, we can only correct $1$ error per codeword sent. 

If we are arrogant and do not heed to the advice of theoreticians,  we can wrongly assert that our codewords can actually correct *two* errors per codeword. This would be a grave mistake because of the following scenario. Suppose we send the message $M = 11010\ \ 01101$ to Earth. The received message, allowing for our tolerance of two errors, might be $M' = \underline{0}1\underline{1}10 \ \ 011\underline{1}\underline{0}$. If we were truly able to correct two errors per codeword, then we must be able to retrieve our original message from $M'$. However, it is impossible to distinguish between the received words, since they are the same.  

[1]

---

To prove Hamming's theorem, consider the set $A$ of all words at most a distance $e$ from a codeword $a$, and similarly the set $B$ of all bit sequences a distance at most $e$ from a codeword $b$, where $a$ and $b$ have distance $2e + 1$. We want to show their intersection $A \cap B$ is empty to prove that there can be no "collisions" when there have been up to $e$ errors under transmission of the codewords. We do not want to have different codewords to end up as the same word after transmission.

Suppose there is an element $x$ in $A \cap B$. $x$ has distance at most $e$ from $A$ and from $B$. If we believe a sort of "triangle inequality" for Hamming distance: $d(a,b) \leq d(a, x) + d(x, b)$ then $2e + 1 \leq e + e = 2e$ which is a contradiction.

More pictorially, we can consider $A$ and $B$ as discs with radius $e$ from centers $a$ and $b$ respectively, with the distance between $a$ and $b$ being $2e+1$. It is pictorially obvious that A and B are disjoint. However, if the radius of each disc is increased by 1, the discs no longer remain disjoint.

<img src="/home/aldalire/text4627-6-5.png" style="zoom:40%" />

<img src="/home/aldalire/text4627-6-5-0.png" style="zoom:40%" />

---

The definition of Hamming distance lets us create a new definition for an error correcting code. It is a set of codewords with the least Hamming distance $2e+1$ to allow it to correct for $e$ errors per codeword. 



## (3) Connection to Projective Geometry

Projective geometry can help us generate codewords.

## (3.1) The Hamming Matrix

To explore the connections between error correcting codes and finite projective planes, we have to define a matrix which can encode information about the code. The **Hamming matrix**  is a way to encode information about the the Hamming code. It is a matrix in $F_2$, the finite field with two elements (Note that addition in $F_2$ is isomorphic to XOR addition $\oplus$). Roughly, it takes a message in $EM'$ as input and outputs a sign which will specify what went wrong under transmission.

To take our example of the Hamming (7,4) code, $xyz$ can be computed as
$$
x = b \oplus c \oplus d \\
y = a \oplus c \oplus d \\ 
z = a \oplus b \oplus d
$$
Our Hamming matrix $H$ arises from this definition 
$$
H = 
\begin{bmatrix}
0 & 1 & 1 & 1 & 1 & 0 & 0 \\
1 & 0 & 1 & 1 & 0 & 1 & 0 \\
1 & 1 & 0 & 1 & 0 & 0 & 1
\end{bmatrix}
$$
It is worth noting that $H$ can be written in block form as $H = \begin{bmatrix} P & I\end{bmatrix}$, where $I$ is the $3\times3$ identity matrix and $P$ is merely a different, more terse way to encode $xyz$ from a given message of four bits by interpreting XOR as addition in $F_2$

Suppose we have a message $0110$ to send to Earth. The check bits are $011$, so $EM = 0110011$, and Earth received $EM' = 011\underline{1}011 = m$. Computing $Hm$, we obtain 
$$
\begin{align*}

\begin{bmatrix}
0 & 1 & 1 & 1 & 1 & 0 & 0 \\
1 & 0 & 1 & 1 & 0 & 1 & 0 \\
1 & 1 & 0 & 1 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
0 \\ 1 \\ 1 \\ 1 \\ 0 \\ 1 \\ 1
\end{bmatrix}
&= 
\begin{bmatrix}
0 & 1 & 1 & 1 \\
1 & 0 & 1 & 1 \\
1 & 1 & 0 & 1
\end{bmatrix}

\begin{bmatrix}
0 \\ 1 \\ 1 \\ 1
\end{bmatrix}
+ 
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
0 \\ 1 \\ 1
\end{bmatrix}
 \\& =
 \begin{bmatrix}
 1 \\ 0 \\ 0
 \end{bmatrix}
 +
  \begin{bmatrix}
 0 \\ 1 \\ 1
 \end{bmatrix}
  =  \begin{bmatrix}
 1 \\ 1 \\ 1
 \end{bmatrix}
 \end{align*}
$$
The ones in each position signifies which check bits are inaccurate, so the $[1\ \ 1\ \ 1]$ signifies that all the check bits differ. Doing the same analysis as in **§1.3**, we can correctly deduce that the last message bit is inaccurate. 

The 8 outputs of $H$ correspond to the error codes in  **§1.3**. 

## (3.2) A Wild Fano Plane Appears

<img src="/home/aldalire/text875.png" style="zoom:40%" />

If we label the points in the Fano plane from $p_1$ to $p_7$, and correspondingly label the lines from $1$ to $7$, we can make a matrix $A$ where $A_{ij}$ equals one if $p_j$ is in line $i$ and zero if not. 

|          | $p_1$ | $p_2$ | $p_3$ | $p_4$ | $p_5$ | $p_6$ | $p_7$ |
| -------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| $p_1p_4$ | 1     |       |       | 1     | 1     |       |       |
| $p_1p_3$ | 1     | 1     | 1     |       |       |       |       |
| $p_1p_7$ | 1     |       |       |       |       | 1     | 1     |
| $p_2p_6$ |       | 1     |       | 1     |       | 1     |       |
| $p_2p_5$ |       | 1     |       |       | 1     |       | 1     |
| $p_3p_5$ |       |       | 1     |       | 1     | 1     |       |
| $p_3p_7$ |       |       | 1     | 1     |       |       | 1     |

This creates 7 codewords. We can create seven more by taking the dual of each codeword: $1001100$ becomes $0110011$. Finally, we can add $0000000$ and $1111111$, to make a total of 16 codewords. If the codewords generated using this method are treated as a vector and multiplied to the Hamming matrix, then the output will be $\begin{pmatrix} 0 & 0 & 0 \end{pmatrix}$ indicating that these are valid codewords. Here's a list of all the codewords

| Codewords | Codewords | Dual Codewords | Dual Codewords |           |
| --------- | --------- | -------------- | -------------- | --------- |
| $1001100$ | $0100101$ | $0110011$      | $1011010$      | $1111111$ |
| $1110000$ | $0010110$ | $0001111$      | $1101001$      | $0000000$ |
| $1000011$ | $0011001$ | $0111100$      | $1100110$      |           |
| $0101010$ |           | $1010101$      |                |           |

## (3.4) The Hamming code is a Perfect Code

A **perfect** code is a set of codewords $A$, each with length $n$, there exists an $e$ such that any bit sequence with length $n$ which is not a codeword is at most $e$ away from a unique codeword. Another way to put this is that if $B_e(a)$ is the set of all bit sequences $e$ away from the codeword $a$, then the *disjoint union* of all $B_e(a)$, where a is a codeword, is the set of all bit sequences of length $n$

The **weight** $w$ is the amount of ones in a codeword. i.e., $w(1001100) = 3$. 

To prove that the Hamming code is perfect, we have to show that any bit sequence which is not a codeword is of length $n$ is a distance $1$ away from any codeword. We can show that the Hamming code is perfect by relying on the geometry of the Fano plane.  

Let $x$ be a bit sequence of length $7$. If $w(x) = 1, 2, 6$ then it is plain that it is a distance $1$ away from a codeword. If $w(x) = 1, 6$, then the nearest codeword is $0000000$, $1111111$ respectively. If $w(x) = 2$, then $x$ represents two points in the Fano plane. Since a pair of points determine a unique line, then that line is the nearest codeword from $x$.

If $w(x) = 3$ and not a codeword, then the points which $x$ represents are 2 points $a, b$ in a line and another point $c$ outside of that line. Note that for any dual codeword, the points that it represents form a quadrilateral in the Fano plane such that no three points are in the same line. This is because all lines in the Fano plane intersect at a point. Therefore, take the unique point $d$ such that $abcd$ forms a quadrilateral in the Fano plane such that no three points are in the same line. That is the nearest codeword

If $w(x) = 4$ and not a codeword, it must be a quadrilateral with three points $a, b, c$in a line, and a single point $d$ not in that line. Therefore, by discarding $d$, the nearest codeword is the three points $a,b,c$ in the line.

If $w(x) = 5$ and not a codeword, one can show that any set of 5 points in the Fano plane is the union of two lines. Then, one can discard the point common to both of those lines, leaving a quadrilateral such that no three points lie in a line.

[4 page 194]

<div style="page-break-after: always;"></div>

# Bibliography

[1] http://www.ams.org/publicoutreach/feature-column/fcarc-errors1

[2] Eigenchris https://www.youtube.com/watch?v=eixCGqdlGxQ&list=PLJHszsWbB6hqkOyFCQOAlQtfzC1G9sf2_&index=1

[3] http://micsymposium.org/mics_2009_proceedings/mics2009_submission_52.pdf

[4] https://www.maths.ed.ac.uk/~v1ranick/papers/beutel.pdf

**Written in Typora**

