%!TEX TS-program = ../make.zsh

\begin{frame}{Example: Reintroduction of a fixed bug}
  \begin{columns}
  \begin{column}{0.6\textwidth}
      \hspace*{-0.5cm}
      \begin{tikzpicture}
        \gitDAG{%
          A -- B -- {
            C -- D [xshift = 2.5cm, fill = green!30] -- E [xshift = 3cm, fill = yellow!30],
            F -- G [fill = red!30] -- {
              H [fill = green!30],
              I [fill = red!30] -- J [fill = red!30]
            }
          },
          B -- F,
          H -- D,
          J -- E
        };

        \node[left = of A, DAGref, fill = yellow!30]{main};
        \node[left = of F, DAGref, fill = yellow!30]{foo};
        \node[left = of I, DAGref, fill = yellow!30]{bar};

        \gittag{introduced bug}{above = 3cm of G, fill = red!30}{G};
        \gittag{fixed bug}{above = 2cm of H, fill = green!30}{H};
        \gittag{bug present?}{above = 1cm of E, fill = yellow!30}{E};

      \end{tikzpicture}
  \end{column}
  \begin{column}{0.4\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item Consider a feature branch `foo` that introduces a \alert{bug} (`G`)
        \item The bug gets \alert{fixed during review} (`H`)
        \item However, you \alert{base work on} the buggy code while waiting for review (`I`, `J`)
        \item When \alert{merging this additional work} into `main` (`E`), does it contain the \alert{bug or the fix?}
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}
\end{frame}

\begin{frame}{Example: Reintroduction of a fixed bug (2-way merges)}
  \begin{columns}
  \begin{column}{0.6\textwidth}
      \hspace*{-0.5cm}
      \begin{tikzpicture}
        \gitDAG{%
          A -- B -- {
            C -- D [xshift = 2.5cm, fill = green!30] -- E [xshift = 3cm, fill = yellow!30],
            F -- G [fill = red!30] -- {
              H [fill = green!30],
              I [fill = red!30] -- J [fill = red!30]
            }
          },
          B -- F,
          H -- D,
          J -- E
        };

        \node[left = of A, DAGref, fill = yellow!30]{main};
        \node[left = of F, DAGref, fill = yellow!30]{foo};
        \node[left = of I, DAGref, fill = yellow!30]{bar};

        \gittag{introduced bug}{above = 3cm of G, fill = red!30}{G};
        \gittag{fixed bug}{above = 2cm of H, fill = green!30}{H};

        \gitblobmc{above = of E};

      \end{tikzpicture}
  \end{column}
  \begin{column}{0.4\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item Consider a feature branch `foo` that introduces a \alert{bug} (`G`)
        \item The bug gets \alert{fixed during review} (`H`)
        \item However, you \alert{base work on} the buggy code while waiting for review (`I`, `J`)
        \item When \alert{merging this additional work} into `main` (`E`), does it contain the \alert{bug or the fix?}
        \item Answer: With \alert{2-way merges}, git can't know and will present a \alert{merge conflict}.
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}
\end{frame}

\begin{frame}{Example: Reintroduction of a fixed bug (3-way merges)}
  \begin{columns}
  \begin{column}{0.6\textwidth}
      \hspace*{-0.5cm}
      \begin{tikzpicture}
        \gitDAG{%
          A -- B -- {
            C -- D [xshift = 2.5cm, fill = green!30] -- E [xshift = 3cm, fill = green!30],
            F -- G [fill = blue!30] -- {
              H [fill = green!30],
              I [fill = red!30] -- J [fill = red!30]
            }
          },
          B -- F,
          H -- D,
          J -- E
        };

        \node[left = of A, DAGref, fill = yellow!30]{main};
        \node[left = of F, DAGref, fill = yellow!30]{foo};
        \node[left = of I, DAGref, fill = yellow!30]{bar};

        \gittag{introduced bug}{above = 3cm of G, fill = red!30}{G};
        \gittag{fixed bug}{above = 2cm of H, fill = green!30}{H};

        \gittag{contains fix}{above = 1cm of E, fill = green!30}{E};

      \end{tikzpicture}
  \end{column}
  \begin{column}{0.4\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item Consider a feature branch `foo` that introduces a \alert{bug} (`G`)
        \item The bug gets \alert{fixed during review} (`H`)
        \item However, you \alert{base work on} the buggy code while waiting for review (`I`, `J`)
        \item When \alert{merging this additional work} into `main` (`E`), does it contain the \alert{bug or the fix?}
        \item Answer: With \alert{2-way merges}, git can't know and will present a \alert{merge conflict}.
        \item With \alert{3-way merges}, git can identify the {\color{blue!30}common ancestor} (`G`) and \alert{resolve the conflict automatically}. `E` contains the \alert{fix}.
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}

  \source{https://github.com/fiedl/icecube-git-migration/issues/15}
\end{frame}

