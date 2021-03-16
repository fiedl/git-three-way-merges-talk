%!TEX TS-program = ../make.zsh

\begin{frame}[fragile]{Trick: `git graph`}
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
      \vspace{1em}
      \begin{onlyenv}<1>
        \textbf{Show the graph in the command line}:
        \small
        \begin{verbatim}
$ git checkout main
$ git log --decorate --oneline --graph
*   G (HEAD -> main) import earlier history
|\
| *   I (temp) merging main into temp
| |\
| |/
|/|
* | C blub
* | B blah
| * H merging F into temp
|/|
| * (earlier-history) F baz
| * E bar
| * D foo
* A baz
        \end{verbatim}
      \end{onlyenv}

      \begin{onlyenv}<2>
        \normalsize
        \textbf{Define `git graph` as alias}:
        \small
        \begin{verbatim}
$ git config \
  --global alias.graph \
  "log --decorate --oneline --graph"

$ git graph
*   G (HEAD -> main) import earlier history
|\
| *   I (temp) merging main into temp
| |\
...
        \end{verbatim}
      \end{onlyenv}
      \normalsize

    \end{column}
  \end{columns}

  \tiny{See also: \url{https://stackoverflow.com/q/1057564/2066546}, \url{https://github.com/fiedl/git-three-way-merges-talk/issues/3}}
\end{frame}
