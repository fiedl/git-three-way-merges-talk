%!TEX TS-program = ../make.zsh

\begin{frame}{Application: Bring in earlier history}
  \begin{columns}
  \begin{column}{0.6\textwidth}
    \begin{minipage}[t][\textheight][t]{\textwidth}
      \vspace*{3cm}

      \begin{tikzpicture}
        \only<1>{
          \gitDAG{%
            A -- B -- C
          };
        }
        \only<2-3>{
          \gitDAG{%
            A -- B -- C,
            D [xshift = -3.0cm] -- E [xshift = -3.0cm] -- F [xshift = -3.0cm]
          };
        }
        \only<4->{
          \gitDAG{%
            A -- B -- C -- G [fill = red!30],
            D [xshift = -3.0cm] -- E [xshift = -3.0cm] -- F [xshift = -3.0cm],
            F -- G
          };
        }

        \node[left = 5mm of A, DAGref, fill = yellow!30]{icetray};
        \only<2->{\node[left = 5mm of D, DAGref, fill = yellow!30]{earlier-history};}

        \only<3->{\draw[dashed] (A)--(F) node [midway, left] {\relates};}

        \visible<5->{\gitblobmc{above = of G};}
      \end{tikzpicture}

      \vspace{2em}
      \visible<4->{\scriptsize
        `git checkout icetray` \\
        `git merge --allow-unrelated-histories --no-ff earlier-history`
      }

    \end{minipage}
  \end{column}
  \begin{column}{0.4\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item Consider the history of \alert{`icetray` imported from svn} beginning at an arbitrary revision
        \item<2-> Later, we might \alert{import earlier history} from svn
        \item<3-> `A` and `F` correspond to the same svn revision, but have \alert{different commit ids} due to the separate imports
        \item<4-> Now we want to \alert{merge the `earlier-history`} into `icetray`
        \item<5-> But as there is no {\color{blue!30} common ancestor}, all \alert{merge conflicts} need to be resolved manually
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}
\end{frame}

\begin{frame}{Application: Bring in earlier history}
  \begin{columns}
  \begin{column}{0.65\textwidth}
    \begin{minipage}[t][\textheight][t]{\textwidth}
      \vspace*{3cm}

      \begin{tikzpicture}

        \only<1>{
          \gitDAG{%
            A -- B -- C,
            H [xshift = 1.5cm, fill = yellow!70],
            D [xshift = -3.0cm] -- E [xshift = -3.0cm] -- F [xshift = -3.0cm],
            A -- H,
            F -- H
          };
        }
        \only<2>{
          \gitDAG{%
            A -- B -- C,
            H [xshift = 1.5cm, fill = yellow!70] -- I [xshift = 3.0cm, fill = yellow!70],
            D [xshift = -3.0cm] -- E [xshift = -3.0cm] -- F [xshift = -3.0cm],
            A -- H,
            F -- H,
            C -- I
          };
        }
        \only<3>{
          \gitDAG{%
            A -- B -- C -- G [fill = red!30, xshift = 1.5cm],
            H [xshift = 1.5cm, fill = yellow!70] -- I [xshift = 3.0cm, fill = yellow!70],
            D [xshift = -3.0cm] -- E [xshift = -3.0cm] -- F [xshift = -3.0cm],
            A -- H,
            F -- H,
            C -- I,
            I -- G
          };
        }

        \node[left = 5mm of A, DAGref, fill = yellow!30]{icetray};
        \node[left = 5mm of D, DAGref, fill = yellow!30]{earlier-history};

        \draw[dashed] (A)--(F) node [midway, left] {\relates};
      \end{tikzpicture}

      \vspace{2em}
      \visible<1->{\scriptsize%
        `git checkout A` \\
        `git merge --allow-unrelated-histories --strategy=ours --no-ff F`
      }%
      \visible<2->{\scriptsize%
        `git merge --no-ff C`
      }

    \end{minipage}
  \end{column}
  \begin{column}{0.35\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item \alert{Connect both histories} and identify corresponding commits (`A` \relates `F` \relates `H`)
        \item<2-> Bring in \alert{newer commits}
        \item<3-> Create a \alert{pull request} for `I` against `icetray` for convenience, e.g. to document the process
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}
\end{frame}
