---
Title: "Σ-Protocols and Commitments from Hardness of Discrete Logarithm"
Status: Done
Level: "3"
---

# Chapter 12: $Σ$-Protocols and Commitments from Hardness of Discrete Logarithm

## 12.1 Cryptographic Background

### 12.1.1 A Brief Introduction to Groups

This is the definition of [group](../../terms/group.md).

### 12.1.2 The Discrete Logarithm Problem and Background on Elliptic Curves

#### 12.1.2.1 Discrete Log Problem

For a specified group $G$ the discrete logarithm problem takes as input two group elements $g$ and $h$, and the goal is to output a
positive integer $i$ such that $g^i = h$ (if $G$ is of prime order then such an $i$ is guaranteed to exist). The discrete logarithm
problem is believed to be computationally intractable in certain groups $G$. An important caveat is that quantum computers can solve
the discrete logarithm problem in polynomial time via Shor’s algorithm. Hence, cryptosystems whose security is based on the assumed
hardness of the discrete logarithm problem are not post-quantum secure.

#### 12.1.2.2 Elliptic Curve Groups

You should take a look at Elliptic curve [here](../../terms/elliptic_curve.md). \
**Algorithms for computing discrete logarithms**: The fastest known classical algorithm to solve the Discrete Logarithm problem over
most elliptic curve groups used in practice runs in time $O(\sqrt{G})$. For example, a popular elliptic curve called Curve25519, which
is defined over base field $F$ of size $2^{255} −19$, defines a cyclic group of order close to $2^{252}$, so the time complexity is
about $O(2^{128})$.  
**Scalar field vs base field**: The field of size equal to the (prime) order of the elliptic curve group $G$ is typically referred to
as the _scalar field_ of $G$. It is not the same as the base field $F$ over which the curve is defined.

## 12.2 Schnorr’s $Σ$-Protocol For Knowledge of Discrete Logarithms

This section covers:

- Establishing that the prover has knowledge of a discrete logarithm of some group element
  ([Section 12.2.2](#12.2.2)).
- Allowing the prover to cryptographically commit to group elements without revealing the committed group element to the verifier until
  later ([Section 12.3](#12.3)).
- Establishing product relationships between committed values
  ([Section 12.3.2](#12.3.2))

### 12.2.1 $Σ$-Protocols

A relation $R$ specifies a collection of “valid” instance-witness pairs $(h,w)$. For example, $h=g^w$ for discrete logarithm relation.\
A $\Sigma$-protocol is a 3-message public coin protocol in which both verifier and prover know a public input $h$, and prover knows a
witness $w$ such that $(h,w)\in R$. Let denote the three messages by $(a, e, z)$, with the prover first sending $a$, the verifier
responding with a challenge $e$ , and the prover replying with $z$. A $Σ$-protocol is required to satisfy perfect completeness, and two
additional properties:

- **Special soundness**: There exists a polynomial time algorithm $Q$ such that, when given as input a pair of accepting transcripts
  $(a, e, z)$ and $(a, e',z')$ with $e \neq e'$ , $Q$ outputs a witness $w$ such that $(h,w) ∈ R$.
- **Honest Verifier Perfect Zero-Knowledge (HVZK)**: There must be a randomized polynomial time simulator that takes as input the
  public input $h$ from the $Σ$-protocol, and outputs a transcript $(a, e,z)$ such that the distribution over transcripts output by the
  simulator is identical to the distribution over transcripts produced by the honest verifier in the $Σ$-protocol interacting with the
  honest prover.

### 12.2.2 Schnorr’s $Σ$-Protocol For the Discrete Logarithm Relation <a id="12.2.2"></a>

The algorithm can be presented as follows:  
$1$: Let $G$ be a (multiplicative) cyclic group of prime order with generator $g$.  
$2$: Public input is $h$ = $g^w$, where only prover knows $w$.  
$3$: Picks a random number $r \in \{0,...,|G| −1\}$ and sends $a ← g^r$ to the verifier.  
$4$: Verifier responds with a random element
$e ∈ \{0,...,|G|−1\}.$  
$5$: Prover responds with $z ← (we+r)$ mod $|G|$.  
$6$: Verifier checks that $a.h^e = g^z$.

Perfect completeness is easy to establish, so we will prove that Schnorr’s protocol satisfies special soundness, and honest-verifier
zero-knowledge.  
**Special soundness**: Suppose we are given two accepting transcripts $(a, e,z)$ and $(a, e' ,z')$ with $e \neq e'$. They imply that we
have $z = we + r$ and $z' = we' + r$, so $w \equiv (z-z').(e-e')^{-1}$ mod $|G|$.  
**Honest-Verifier Perfect Zero Knowledge**: The simulator selects $e$ uniformly at random from $\{0,...,|G| −1\}$ and samples $z$
uniformly at random from $\{0,...,|G| −1\}.$ Finally, the simulator sets $a \leftarrow{} g^z ·(h^e)^{−1}$ .

### 12.2.3 Fiat-Shamir Applied to $Σ$-Protocols

You should read the definition of Fiat-Shamir [here](../../terms/fiat_shamir.md).

For concreteness, we couch the presentation in the context of Schnorr’s protocol. Recall that in the resulting non-interactive
argument, the honest prover aims to produce an accepting transcript $(a, e,z)$ for the Σ-protocol, where $e = R(h,a)$ and $R$ denotes
the random oracle. Let $I$ refer to the $Σ$-protocol and $Q$ refer to the non-interactive argument obtained by applying the Fiat-Shamir
transformation to $I$. Let $P_{FS}$ be a prover for $Q$ that produces a convincing proof on input $h$ with probability at least
$\varepsilon$. That is, when $P_{FS}$ is run on input $h$, it outputs a transcript $(a, e,z)$ that, with probability at least
$\varepsilon$, is an accepting transcript for $I$ and satisfies $e = R(h,a)$.

**The witness extraction procedure**: First, fix the value of any internal randomness used by $P_{FS}$. The first transcript is
obtained by simply running $P_{FS}$ once, generating a random value for $R$’s response to each query $P_{FS}$ makes to the random
oracle. This yields a transcript $(a, e,z)$ satisfying $e = R(h,a)$ such that with probability at least $\varepsilon$ the transcript is
an accepting one for $I$. By assumption, during this execution of $P_{FS}$, exactly one of the $T$ queries to $R$ was equal to $(h,a).$
Rewind $P_{FS}$ to just before it queries $R$ at $(h,a)$, and change $R$’s response to this query from $e$ to a fresh randomly chosen
value $e'$ . Then run $P_{FS}$ once again to completion (again generating a random value from $R$’s response to each query made by
$P_{FS}$ ), and hope that $P_{FS}$ outputs an accepting transcript of the from $(a, e' ,z' )$.

**Analysis of the witness extraction procedure**: The probability this procedure outputs two accepting transcripts of the form $(a,
e,z)$ and $(a, e' ,z' )$ with $e \neq e'$ is at least $Ω(\varepsilon ^3/T^2 )$. Note that $e$ will not equal $e'$ with probability
$1−1/2^λ$ , where $λ$ denotes the number of bits output by $R$ on any query.

## 12.3 A Homomorphic Commitment Scheme<a id="12.3"></a>

You should read the definition of commitment scheme [here](../../terms/commitment_scheme.md).

**A Perfectly Hiding Commitment Scheme from any $Σ$-Protocol**: Damgård showed how to use any $Σ$-protocol for any hard relation to
obtain a perfectly hiding, computationally binding commitment scheme. To ensure hiding, Damgård’s transformation does require the
simulator used to establish HVZK must be able to take as input not only the public input $h$, but also a challenge $e^*$ , and output a
transcript $(a, e^* ,z)$.

Here is how Damgård’s commitment scheme works. Let denote $(h,w) ← Gen$, and declares $h$ to be both the committing key $ck$ and the
verification key $vk$ (the "toxic waste" witness $w$ must be discarded because anyone who knows $w$ may be able to break binding of the
commitment scheme). To commit to a message $m$, the committer runs the simulator from the $Σ$-protocol on public input $h$ to generate
a transcript in which the challenge is the message $m$. Let $(a, e,z)$ be the output of the simulator. The committer sends $a$ as the
commitment, and keeps $e = m$ and $z$ as opening information. In the verification stage for the commitment scheme, the committer sends
the opening information $e = m$ and $z$ to the verifier, who uses the verification procedure of the $Σ$-protocol to confirm that $(a,
e,z)$ is an accepting transcript for public input $h$.

By instantiating Damgård’s construction with Schnorr’s $Σ$-protocol for the discrete logarithm relation, one recovers a well-known
commitment scheme due to Pedersen. In the traditional presentation, to commit to a message $m$, the committer picks a random exponent
$z$ in $\{0,...,|G| −1\}$ and the commitment is $g^m · h^z$ . One thinks of $h^z$ as a random group element that operates as a
“blinding factor”: by multiplying $g^m$ by $h^z$ , one ensures that the commitment is a random group element, statistically independent
of $m$. Below is the traditional presentation of Pedersen commitments:

---

**Protocol 5** Standard presentation of Pedersen commitments in a cyclic group $G$ for which the Discrete Logarithm problem is
intractable.

---

$1$: Let $G$ be a (multiplicative) cyclic group of prime order. The key generation procedure publishes randomly chosen generators $g,h
∈ G$, which serve as both the commitment key and verification key.  
$2$: To commit to a number $m ∈ \{0,...,|G| −1\}$, committer picks a random $z ∈ \{0,...,|G| −1\}$ and sends $c ← g^m · h^z$ .  
$3$: To open a commitment $c$, committer sends $(m,z)$. Verifier checks that $c = g^m · h^z$ .

---

Pedersen commitment scheme obtained from Schnorr’s protocol via Damgård’s transformation:

---

**Protocol 6** Commitment scheme obtained from Schnorr’s protocol via Damgård’s transformation.

---

$1$: Let $G$ be a (multiplicative) cyclic group of prime order.  
$2$: The key generation procedure publishes randomly chosen generators $g,h ∈ G$, which serve as both the commitment key and
verification key.  
$3$: To commit to a number $m ∈ \{0,...,|G| −1\}$, committer picks a random $z ∈ \{0,...,|G| −1\}$ and sends $c ← h^{−m} · g^z$  
$4$: To open a commitment $c$, committer sends $(m,z).$ Verifier checks that $c · h^m = g^z$

---

### 12.3.1 Important Properties of Pedersen Commitments

**Additive Homomorphism**: This means that the verifier can take two commitments $c_1$ and $c_2$, to values $m_1,m_2 ∈ \{0,...,|G| −
1\}$ (with $m_1,m_2$ known to the committer but not to the verifier), and the verifier on its own can derive a commitment $c_3$ to $m_3
:= m_1 + m_2$, such that the prover is able to open $c_3$ to $m_3$. This is done by simply letting $c_3 ← c_1 · c_2$. As for "opening
information" provided by the prover, if $c_1 = h^{m_1} · g^{z_1}$ and $c_2 = h^{m_2} · g^{z_2}$ , then $c_3 = h^{m_1+m_2} ·
g^{z_1+z_2}$, so the opening information for $c_3$ is simply $(m_1 + m_2,z_1 + z_2).$

**Perfect HVZK Proof of Knowledge of Opening**: It will be useful for the prover to prove that it knows how to open a commitment $c$ to
some value, without actually opening the commitment. Let's see it:

---

**Protocol 7** Zero-Knowledge Proof of Knowledge of Opening of Pedersen Commitment

---

$1$: Let $G$ be a (multiplicative) cyclic group of prime order over which the Discrete Logarithm relation is hard, with randomly chosen
generators $g$ and $h$.  
$2$: Input is $c = g^m · h^z$ . Prover knows $m$ and $z$, Verifier only knows $c,g,h$.  
$3$: Prover picks $d,r ∈ \{0,...,|G| −1\}$ and sends to verifier $a ← g^d · h^r$ .  
$4$: Verifier sends challenge $e$ chosen at random from $\{0,...,|G| −1\}$.  
$5$: Prover sends $m' ← me+d$ and $z' ← ze+r$. $6$: Verifier checks that $g^{m'} · h^{z'} = c^e · a$.

---

---

**Protocol 8** Equivalent Exposition of Protocol 7 in terms of commitments and additive homomorphism.

---

$1$: Let $G$ be a (multiplicative) cyclic group of prime order over which the Discrete Logarithm relation is hard, with randomly chosen
generators $g$ and $h$.  
$2$: Let $Com(m,z)$ denote the Pedersen commitment $g^m · h^z$ . Prover knows $m$ and $z$, Verifier only knows $Com(m,z),g,h$.  
$3$: Prover picks $d,r ∈ \{0,...,|G| −1\}$ and sends to verifier $a ← Com(d,r).$  
$4$: Verifier sends challenge $e$ chosen at random from ${0,...,|G| −1}.$  
$5$: Let $m' ← me + d$ and $z' ← ze + r$, and let $c' ← Com(m ' ,z ' ).$ While Verifier does not know $m '$ and $z '$ , Verifier can
derive $c '$ unaided from $Com(m,z)$ and $Com(d,r)$ using additive homomorphism: $c'=Com(m,z)^e \cdot Com(d,r)$  
$6$: Prover sends $(m ' ,z' ).$  
$7$: Verifier checks that $m' ,z'$ is valid opening information for $c'$ , i.e., that $g^{m'} · h^{z'} = c'$ .

---

**Perfect Completeness**: $g^{m'} · h^{z'} = g^{me+d} · h^{ze+r} = c^e · a$ **Special Soundness**: Given two accepting transcripts $(a,
e,(m'_1 ,z'_1 ))$ and $(a, e',(m'_2 ,z'_2 ))$ with $e \neq e'$ , we have to extract a valid opening $(m,z)$ for the commitment $c$,
i.e., $g^m · h^z = c$. As in the analysis of the $Σ$-protocol for the Discrete Logarithm relation, we have:

$$
\begin{aligned} m^∗ = (m'_1 −m'_2 )·(e−e')^{−1} \quad mod \quad |G|, \\
z^∗ = (z'_1 −z'_2 )·(e−e')^{−1} \quad mod \quad |G|.
\end{aligned}
$$

Then:

$$
\begin{aligned} g^{m^{*}}\cdot
h^{z^{*}}=\Big(g^{(m_{1}^{\prime}-m_{2}^{\prime})}h^{(z_{1}^{\prime}-z_{2}^{\prime})}\Big)^{(e-e^{\prime})^{-1}}=\Big(c^{e}\cdot
a\cdot\Big(c^{e^{\prime}}\cdot a\Big)^{-1}\Big)^{(e-e^{\prime})^{-1}}=c, \end{aligned}
$$

where the penultimate equality follows from the fact that $(a, e,(m'_1 ,z'_1 ))$ and $(a, e',(m'_1 ,z'_1 ))$ are accepting transcripts.
That is, $(m^* ,z^* )$ is a valid (message, opening information) pair for the commitment $c$.  
**Perfect HVZK**: The simulator samples $e,m' ,z'$ uniformly at random from $\{0,...,|G| −1\}$ and then sets $a ← g^{m'} · h^{z'}·
c^{−e}$ and outputs ($a, e,(m' ,z' )).$

### 12.3.2 Establishing A Product Relationship Between Committed Values<a id="12.3.2"></a>

Unfortunately, Pedersen commitments are not _multiplicatively_ homomorphic: there is no way for the verifier to derive a commitment to
$m_1 · m_2$ without help from the committer. But suppose the committer sends a commitment $c_3$ that is claimed to be a commitment to
value $m_1 ·m_2$ (meaning that the prover knows how to open up $c_3$ to the value $m_1 · m_2$). Is it possible for the prover to prove
to the verifier that $c_3$ indeed commits to $m_1 ·m_2$, without actually opening up $c_3$ and thereby revealing $m_1 ·m_2$? The answer
is yes, using a somewhat more complicated variant of the $Σ$-protocols we have already seen.

---

**Protocol 9** Zero-Knowledge PoK of Opening of Pedersen Commitments Satisfying Product Relationship

---

$1$: Let $G$ be a (multiplicative) cyclic group of prime order over which the Discrete Logarithm relation is hard.  
$2$: Input is $c_i = g^{m_i} · h^{r_i}$ for $i ∈ \{1,2,3\}$ such that $m_3 = m_1 ·m_2$ mod $|G|$  
$3$: Prover knows $m_i$ and $r_i$ for all $i ∈ \{1,2,3\},$ Verifier only knows $c_1, c_2, c_3,g,h$.  
$4$: Prover picks $b_1,...,b_5 ∈ \{0,...,|G| −1\}$ and sends to verifier three values: $α ←
g^{b_1} · h^{b_2} , β ← g^{b_3} · h^{b_4} , γ ← c_1^{b_3} · h^{b_5}$ .  
$5$: Verifier sends challenge $e$ chosen at random from $\{0,...,|G| −1\}$.  
$6$: Prover sends $z_1 ← b_1 +e ·m_1, z_2 ← b_2 +e ·r_1, z_3 ← b_3 +e ·m_2, z_4 ← b_4 +e ·r_2, z_5 ← b_5 +e ·(r_3 −r_1m_2).$  
$7$: Verifier checks that the following three equalities hold:

$$
\begin{aligned} g^{z_1} · h^{z_2} = α · c_1^e,\\
g^{z_3} · h^{z_4} = β · c_2^e ,\\
c_1^{z_3} · h^{z_5} = γ · c_3^e \end{aligned}
$$

---

---

**Protocol 10** Equivalent description of Protocol 9 in terms of commitments and additive homomorphism. The notation $Com_{g,h}(m,z) :=
g^mh^z$ indicates that the group generators used to produce the Pedersen commitment to $m$ with blinding factor $z$ are $g$ and $h$.

---

$1$: Let $G$ be a (multiplicative) cyclic group of prime order over which the Discrete Logarithm relation is hard.  
$2$: Input is $c_i = g^{m_i} · h ^{r_i} = Com_{g,h}(m_i ,r_i)$ for $i ∈ \{1,2,3\}$ such that $m_3 = m_1 ·m_2$ mod $|G|.$  
$3$: Prover knows $m_i$ and $r_i$ for all $i ∈ \{1,2,3\},$ Verifier only knows $c_1, c_2, c_3,g,h$.  
$4$: Prover picks $b_1,...,b_5 ∈ \{0,...,|G| −1\}$ and sends to
verifier three values: $α ← Com_{g,h}(b_1,b_2), β ← Com_{g,h}(b_3,b_4), γ ← Com_{c_1,h}(b_3,b_5).$  
$5$: Verifier sends challenge $e$ chosen at random from $\{0,...,|G| −1\}.$  
$6$: Let $z_1 ← b_1 +e ·m_1, z_2 ← b_2 +e ·r_1, z_3 ← b_3 +e ·m_2, z_4 ← b_4 +e ·r_2, z_5 ← b_5 +e ·(r_3 −r_1m_2).$  
$7$: While Verifier does not know $z_1,...,z_5$, using additive homomorphism Verifier can derive the following three commitments
unaided using additive homomorphism:

$$
\begin{aligned} c_1' = Com_{g,h}(z_1,z_2) = α · c_1^e , \\
c_2' = Com_{g,h}(z_3,z_4) = β · c_2^e , \\
c_3' = Com_{c_1,h}(z_3,z_5) = γ · c_3^e \end{aligned}
$$

This final equality for $c_3'$ exploits that:

$$ \begin{aligned} c*3^e = g^{em_1m_2} h^{er3} = c_1^{em_2} h^{er_3−er_1m_2} = Com*{c_1,h}(em_2, er_3 −er_1m_2). \end{aligned} $$

$8$: Prover sends $z_1,...,z_5.$  
$9$: Verifier checks that:

- $(z_1,z_2)$ is valid opening information for $c_1'$ using generators $g,h$.
- $(z_3,z_4)$ is valid opening information for $c_2'$ using generators $g,h$.
- $(z_3,z_5)$ is valid opening information for $c_3'$ using generators $c_1,h$

---
