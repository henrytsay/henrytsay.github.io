---
title: "Cahier of ($\\LaTeX$) grievances"
date: 2026-10-30
permalink: /posts/2026/10/cahier-of-latex-grievances/
---

LaTeX is a powerful typesetting software, and it is the standard for academic technical writing. Despite its strengths, I still find that it is painful to use. When voicing this complaint, it's common to hear people who are more experienced with LaTeX to say it's just a "skill issue," and that LaTeX is great as is. I believe that we are suffering from a sort of LaTeX Stockholm syndrome, and that things don't really need to be this way. To think about solutions, we must first identify the problems. In this post, I will enumerate some of the biggest problems I have with LaTeX.

## 1. Cryptic error messages

Every once in a while, you will encounter a LaTeX rendering error that will be completely incomprehensible, forcing you to spend longer than necessary searching for the bug that the error message is unhelpful for.

```latex
\documentclass{article}
\begin{document}
The output is saved to my_file.txt.
\end{document}
```

```text
! Missing $ inserted.
<inserted text>
                $
l.3 The output is saved to my_
                              file.txt.
I've inserted a begin-math/end-math symbol since I think
you left one out. Proceed, with fingers crossed.

! Missing $ inserted.
<inserted text>
                $
l.4 \end{document}

I've inserted a begin-math/end-math symbol since I think
you left one out. Proceed, with fingers crossed.
```

The actual problem is that `_` needs to be escaped as `\_`.

## 2. Errors in included files

When you include a section from a different file with `\input{section}`, errors will not clearly tell you which file the error occurred in. The line number is for the included file, but the file name is only indicated by an unclosed parenthesis somewhere above the error in the log. Many editors (like Overleaf) get this wrong and just point you to the `\input` line.

```latex
% main.tex
\documentclass{article}
\begin{document}
\input{intro}
\input{methods}
\input{results}
\end{document}
```

```text
(./intro.tex) (./methods.tex) (./results.tex
! Missing $ inserted.
<inserted text>
                $
l.5 The output is saved to my_
                              file.txt.
```

## 3. The backslash

The choice of using `\` to start commands is annoying. It is in an awkward position on the keyboard and makes typing LaTeX incredibly unpleasant.

```latex
\frac{1}{\sqrt{(2\pi)^k|\boldsymbol{\Sigma}|}}\exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu})^\top\boldsymbol{\Sigma}^{-1}(\mathbf{x}-\boldsymbol{\mu})\right)
```

If you're typing in home row position, it means you need to keep reaching over with your pinky to start each command.

## 4. Too forgiving

LaTeX is too forgiving in how source code is formatted. All of these render identically:

```latex
\int_{0}^{1} \frac{1}{2} x^{2} \, dx

\int_0^1 \frac12 x^2 \, dx

\int^1_0\frac1 2x^2\,dx

\int_0^1\frac{1}2x^2\,dx
```

LaTeX allows you to skip braces when the arguments are a single character, but this causes weird behavior when you have two digit numbers because it just takes the first character for each argument instead of treating the digits as part of a single number.

```latex
\int_0^10 f(x)\,dx
```
 
$$\int_0^10 f(x)\,dx$$
 
```latex
x^10
```
 
$$x^10$$
 
```latex
e^-x
```
 
$$e^-x$$
 
```latex
\frac123
```
 
$$\frac123$$

Spaces don't matter most of the time, except you have to remember that when it's after a command it does.

```latex
\sin x    % sin x
\sinx     % ! Undefined control sequence.
```

## 5. Source code readability

Source code readability is evidently not a priority. Take the following equation.

$$f_n(x)=\left.\frac{\partial^2}{\partial t^2}\right|_{t=0}\sqrt[3]{\frac1{1+\frac{t^2}{\sum_{k=1}^nk^{-s}}}}^2+\int_0^x\frac{e^{-u^2/2}}{\sqrt{2\pi}}\,du$$

The raw source LaTeX for this is:

```latex
f_n(x)=\left.\frac{\partial^2}{\partial t^2}\right|_{t=0}\sqrt[3]{\frac1{1+\frac{t^2}{\sum_{k=1}^nk^{-s}}}}^2+\int_0^x\frac{e^{-u^2/2}}{\sqrt{2\pi}}\,du
```

If you were looking at the code for this equation for the first time, it would be incredibly difficult to decipher this without rendering it.

## 6. Extensive preamble and package inclusions

Every time I begin a new LaTeX document, I struggle to remember which packages are necessary and how to define all the typical settings you want in the preamble. This may be an experience thing, but I believe LaTeX should just have better defaults.

```latex
\documentclass[11pt]{article}
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage[margin=1in]{geometry}
\usepackage{amsmath, amssymb, amsthm}
\usepackage{graphicx}
\usepackage{booktabs}
\usepackage{microtype}
\usepackage{xcolor}
\usepackage{hyperref}   % should be loaded (almost) last
\usepackage{cleveref}   % ...except this one, which goes after hyperref
```

## 7. Poor command names

For example, why is it `\cup` instead of `\union`? No serious mathematician calls it a cup, so this is something that would just need to be memorized by someone using LaTeX for the first time. For many symbols, there is an intuitive command that comes to mind, but LaTeX often has some weird name for it for no particular reason.

| Symbol | Meaning | You might guess | LaTeX |
|:---:|---|---|---|
| ∪ | union | `\union` | `\cup` |
| ∩ | intersection | `\intersection` | `\cap` |
| ∧ | logical and | `\and` | `\wedge` or `\land` |
| ∨ | logical or | `\or` | `\vee` or `\lor` |
| ∇ | gradient | `\grad` | `\nabla` |
| ∘ | composition | `\compose` | `\circ` |
| ∣ | divides | `\divides` | `\mid` |


## 8. Tables
 
Tables/matrices are miserable to write. Cells are separated by `&` (which is annoying to type), rows end with `\\`, and more complex grids need extra packages. Also, the column structure is just defined by the `&` symbols, so a single missed `&` breaks the table, and LaTeX doesn't always tell you.
 
```latex
\begin{tabular}{lcc}
\toprule
Model & Accuracy & F1 \\
\midrule
Baseline & 0.81 & 0.77 \\
Ours & 0.89 0.85 \\
\bottomrule
\end{tabular}
```
 
A missing `&` in the last row compiles perfectly fine, but both numbers just end up in the same cell:
 
| Model    | Accuracy  | F1   |
|----------|:---------:|:----:|
| Baseline | 0.81      | 0.77 |
| Ours     | 0.89 0.85 |      |
 
An extra `&`
 
```latex
Ours & & 0.89 & 0.85 \\
```
 
gives an error that says the problem was fixed for you:
 
```text
! Extra alignment tab has been changed to \cr.
<recently read> \endtemplate
 
l.18 Ours & & 0.89 &
                     0.85 \\
```
 
But the "fix" is to start a new row, so the table comes out like this:
 
| Model    | Accuracy | F1   |
|----------|:--------:|:----:|
| Baseline | 0.81     | 0.77 |
| Ours     |          | 0.89 |
| 0.85     |          |      |


## It doesn't have to be like this

These pain points aren't fundamental, unsolvable problems. We have just gotten used to them because knowing LaTeX is necessary to survive in the academic world. However, being used to it shouldn't stop us from imagining something better.