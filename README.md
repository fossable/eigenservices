Eigenservices is a private compute framework that provides end-to-end security of SaaS-style
applications.

## Motivation

End-to-end encryption has become standard in private messaging which ensures that
data remains secure even in the event that the hosts mediating messages is compromised.

We would like to extend this feature further into the realm of SaaS applications, where
not only is the data encrypted, but the entire application as well! Thus a complete compromise
of the host where an application is running does not entail a compromise of the application
itself.

## Theory

As the name suggests, the theory behind `eigenservices` is founded in concepts of
Linear Algebra. In non-technical terms, an `eigenservice` is a program that can run
on untrusted hosts without giving away sensitive information by "encrypting" the
program's memory and "encrypting" its machine code.

### Matrix Machine State Representation (MMSR)

In order to operate on machine states with matrix transformations, we first need
a way to represent the state of the machine as a matrix.

For CPU registers, we collect register values into a square matrix arbitrarily. For an
example CPU with four registers,

$$RS = \begin{bmatrix} \%rax & \%rbx \\ \%rcx & \%rdx \end{bmatrix}$$

is one such option. The matrix must have equal row and column dimensions and can be
padded with placeholder values if necessary.

For program memory, we collect addresses sequentially into a sparse matrix:

$$MS = \begin{bmatrix}0x0001 & ... & ...\\... & ... & ...\\... & ... & 0xFFFF\end{bmatrix}$$

### Matrix Instruction Transformation Representation (MITR)

CPU instructions can be represented as transformations to the register state (RS)
or memory state (MS). For example, the instruction:

```
add $1,%rax
```

is represented as

$$MR_{i + 1} = MR_{i} + \begin{bmatrix}1 & 0\\0 & 0\end{bmatrix}$$

in our previous four-register example CPU.

### Change of Basis

The central idea is to use a secret invertible square matrix $K$ to alter the basis
of both the MMSR and MITR such that an adversary can no longer inspect the state of
the program (RS and MS) nor the instructions being executed.

An adversary with access to the program running the modified basis would not be able
to acertain anything useful without knowledge of the secret basis transformation matrix
$K$ (or more specifically, $K^{-1}$).
