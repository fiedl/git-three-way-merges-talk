%!TEX TS-program = ../make.zsh

\begin{frame}{Application: Merge feature from other history}
  \begin{columns}
  \begin{column}{0.6\textwidth}
    \begin{minipage}[t][\textheight][t]{\textwidth}
      \vspace*{3cm}

      \begin{tikzpicture}
        \only<1>{
          \gitDAG{%
            A -- B -- C -- D -- E -- F,
            G [xshift = 1.5cm] -- H [xshift = 1.5cm],
            I [xshift = 4.5cm] -- J [xshift = 4.5cm],
            H -- I
          };
        }
        \only<2->{
          \gitDAG{%
            A -- B -- C -- D -- E -- F -- K [fill = red!30],
            G [xshift = 1.5cm] -- H [xshift = 1.5cm],
            I [xshift = 4.5cm] -- J [xshift = 4.5cm],
            H -- I,
            J -- K
          };
        }

        \node[left = 5mm of A, DAGref, fill = yellow!30]{icetray};
        \node[left = 5mm of G, DAGref, fill = yellow!30]{clsim};
        \node[right = 5mm of J, DAGref, fill = yellow!30]{hole-ice};

        \draw[dashed] (B)--(G) node [midway, left] {\relates};
        \draw[dashed] (C)--(H) node [midway, left] {\relates};
      \end{tikzpicture}

      \only<3->{
        \vspace{1em}
        {\scriptsize%
          `git checkout icetray` \\
          `git merge --allow-unrelated-histories hole-ice`
        }%
      }
    \end{minipage}
  \end{column}
  \begin{column}{0.4\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item Consider a \alert{feature} (`hole-ice`) \alert{based on other history} (`clsim`) than the main one (`icetray`)
        \item The \alert{histories are mirrored}, but have different commit ids (`B` \relates `G`, `C` \relates `H`)
        \item<2-> \alert{Want to merge} `hole-ice` into `icetray`
        \item<3-> Without a {\color{blue!30} common ancestor}, this is a \alert{2-way merge} and all \alert{merge conflicts} must be resolved manually

        \item<4-> Need to \alert{connect histories} first in order to create a {\color{blue!30} common ancestor}
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}
\end{frame}

\begin{frame}{Application: Merge feature from other history}
  \begin{columns}
  \begin{column}{0.65\textwidth}
    \begin{minipage}[t][\textheight][t]{\textwidth}
      \vspace*{3cm}

      \begin{tikzpicture}
        \only<1>{
          \gitDAG{%
            A -- B -- C -- D -- E -- F,
            L [xshift = 4.5cm, fill = yellow!70],
            G [xshift = 1.5cm] -- H [xshift = 1.5cm],
            I [xshift = 4.5cm] -- J [xshift = 4.5cm],
            H -- I,
            C -- L,
            H -- L
          };
        }
        \only<2>{
          \gitDAG{%
            A -- B -- C -- D -- E -- F,
            L [xshift = 4.5cm, fill = yellow!70] -- M [xshift = 7.5cm, fill = yellow!70],
            G [xshift = 1.5cm] -- H [xshift = 1.5cm],
            I [xshift = 4.5cm] -- J [xshift = 4.5cm],
            H -- I,
            C -- L,
            H -- L,
            F -- M,
            J -- M
          };
        }
        \only<3>{
          \gitDAG{%
            A -- B -- C [fill = blue!30] -- D -- E -- F,
            L [xshift = 4.5cm, fill = yellow!70] -- M [xshift = 7.5cm, fill = yellow!70],
            G [xshift = 1.5cm] -- H [xshift = 1.5cm, fill = blue!30],
            I [xshift = 4.5cm] -- J [xshift = 4.5cm],
            H -- I,
            C -- L,
            H -- L,
            F -- M,
            J -- M
          };
        }
        \only<4->{
          \gitDAG{%
            A -- B -- C [fill = blue!30] -- D -- E -- F -- K [fill = red!30, xshift = 1.5cm],
            L [xshift = 4.5cm, fill = yellow!70] -- M [xshift = 7.5cm, fill = yellow!70],
            G [xshift = 1.5cm] -- H [xshift = 1.5cm, fill = blue!30],
            I [xshift = 4.5cm] -- J [xshift = 4.5cm],
            H -- I,
            C -- L,
            H -- L,
            F -- M,
            J -- M,
            M -- K
          };
        }

        \node[left = 5mm of A, DAGref, fill = yellow!30]{icetray};
        \node[left = 5mm of G, DAGref, fill = yellow!30]{clsim};
        \node[right = 5mm of J, DAGref, fill = yellow!30]{hole-ice};

        \draw[dashed] (B)--(G) node [midway, left] {\relates};
        \draw[dashed] (C)--(H) node [midway, left] {\relates};
      \end{tikzpicture}

      \only<1->{\scriptsize
        `git checkout C` \\
        `git merge --allow-unrelated-histories --strategy=ours --no-ff H`
      }
      \only<2->{\scriptsize
        `git merge icetray hole-ice`
      }

    \end{minipage}
  \end{column}
  \begin{column}{0.35\textwidth}
    \begin{minipage}[t][0.8\textheight][t]{\textwidth}
      \begin{itemize}
        \item \alert{Connect both histories} and identify two corresponding commits (`C` \relates `H` \relates `L`)
        \item<2-> \alert{Bring in changes} of `icetray` and `hole-ice`: `M` contains current `icetray` as well as `hole-ice`.
        \item<3-> Merge conflicts are \alert{resolved automatically} as git knows the {\color{blue!30} common ancestors} (`C` for merging `F`, `H` for merging `J`)
        \item<4-> To see a nice diff and use review tools, create a \alert{pull request} for `M` against `icetray`.
      \end{itemize}
    \end{minipage}
  \end{column}
  \end{columns}

  \source{https://github.com/fiedl/icecube-git-migration/issues/14}
\end{frame}
