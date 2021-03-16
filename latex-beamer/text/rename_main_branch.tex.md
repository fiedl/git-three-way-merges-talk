%!TEX TS-program = ../make.zsh

\begin{frame}[fragile]{Cave: Avoid renaming the `main` branch}
  \label{slide:rename_main}

  \begin{columns}
  \begin{column}{0.5\textwidth}
    \begin{tikzpicture}
      % Git merge
      \only<1-2>{
        \gitDAG{
          A -- B -- { E [xshift=2.5cm] ,
            C -- D}
        };
      }
      \only<1-2>{
        \gitbranch{main}{above = of E}{E};
      }
      \only<1>{
        \gitbranch{feature}{below = of D}{D};
      }
      \only<2>{
        \gitDAG{
          A -- B -- { E [xshift=2.5cm] ,
            C -- D -- F},
            E -- F
        };
        \gitbranch{main}{above = of E}{E};
        \gitbranch{feature}{below = of F}{F};
      }
      \only<3->{
        \gitDAG{
          A -- B -- { E [xshift=2.5cm] ,
            C -- D -- F},
            E -- F
        };
        \gitbranch{feature}{below = of F}{F};
        \gitbranch{main}{below = of feature}{feature};
      }
    \end{tikzpicture}

    \only<4>{
      \vspace{1cm} The `main` branch now contains the commits `A B C D F` rather than `A B E`.

      \vspace{1em} This breaks \goal{bisection} and \goal{readable changelogs}.
    }
  \end{column}
  \begin{column}{0.5\textwidth}
    \header{What is renaming the `main` branch?}

    \begin{itemize}
      \item<1->Consider a `main` branch and a `feature` branch.
      \item<2->The author of the `feature` branch pulls in the `main` branch to get the changes that have been committed to the `main` branch in the meantime:

        `git checkout feature` \\
        `git merge main`
      \item<3-> The author of the `feature` branch merges his feature branch in to `main`, which would be a fast-forward merge at this point.

        `git checkout main` \\
        `git merge feature`
    \end{itemize}
  \end{column}
  \end{columns}

\end{frame}

\begin{frame}[fragile]{Cave: Avoid renaming the `main` branch}

  \begin{column}{0.45\textwidth}
    \begin{tikzpicture}
      \only<1>{
        \gitDAG{
          A -- B -- { E [xshift=2.5cm] ,
            C -- D -- F},
            E -- F
        };
        \gitbranch{main}{above = of E}{E};
        \gitbranch{feature}{below = of F}{F};
      }
      \only<2>{
        \gitDAG{
          A -- B -- { E [xshift=2.5cm] -- G [xshift=2.5cm],
            C -- D -- F},
            E -- F,
            F -- G
        };
        \gitbranch{feature}{below = of F}{F};
        \gitbranch{main}{above = of G}{G};
      }
    \end{tikzpicture}
  \end{column}
  \begin{column}{0.55\textwidth}
    \begin{itemize}
      \item Suppose you need to keep all commits of the feature branch because they are already pushed.
      \item Use `--no-ff` to avoid accidental fast-forward merges:

        `git checkout main` \\
        `git merge --no-ff feature`
    \end{itemize}
  \end{column}

\end{frame}
