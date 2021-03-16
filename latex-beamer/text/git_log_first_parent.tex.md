%!TEX TS-program = ../make.zsh

\begin{frame}[fragile]{Trick: `git log` only history of `main`}
  \begin{columns}
    \begin{column}{0.6\textwidth}
      \begin{tikzpicture}
          \gitDAG{%
            A -- B -- C -- G [fill = red!30, xshift = 1.5cm],
            H [xshift = 1.5cm, fill = yellow!70] -- I [xshift = 3.0cm, fill = yellow!70],
            D [xshift = -3.0cm] -- E [xshift = -3.0cm] -- F [xshift = -3.0cm],
            A -- H,
            F -- H,
            C -- I,
            I -- G
          };

        \node[left = 5mm of A, DAGref, fill = yellow!30]{main};

        \draw[dashed] (A)--(F) node [midway, left] {\relates};
      \end{tikzpicture}
    \end{column}
    \begin{column}{0.4\textwidth}
      \begin{onlyenv}<1>
        \textbf{By default}:
        \begin{verbatim}
$ git checkout main
$ git log
G import earlier history
I merging main into temp
C blub
H merging F into temp
B blah
F baz
A baz
E bar
D foo
        \end{verbatim}
      \end{onlyenv}

      \begin{onlyenv}<2>
        \textbf{More conveniently}:
        \begin{verbatim}
$ git checkout main
$ git log --first-parent
G import earlier history
C blub
B blah
A baz
        \end{verbatim}

        \textbf{With alias:}
        \begin{verbatim}
$ git config --global alias.lg "log --first-parent"
$ git lg
        \end{verbatim}

      \end{onlyenv}

    \end{column}
  \end{columns}
\end{frame}
