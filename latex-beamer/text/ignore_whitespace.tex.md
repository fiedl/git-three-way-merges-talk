%!TEX TS-program = ../make.zsh

\begin{frame}[fragile]{Cave: Avoid renaming the `main` branch}
  \label{slide:rename_main}

  \begin{columns}
  \begin{column}{0.5\textwidth}
    \only<1>{\image{whitespace-pullrequest-screenshot}}
    \only<2>{\image{github-whitespace-option}}
  \end{column}
  \begin{column}{0.5\textwidth}
    \begin{itemize}
      \item Don't assume that all developers will agree on one whitespace convention.
      \item But in diffs, it's sometimes hard to see the actual changes due to whitespace clutter.
      \item Ignore whitespace in diffs and automatic conflict resolution:

        `git merge -X ignore-all-space ...`

      \item<2> This can also be used in pull requests and diffs on github by clicking \alert{Hide whitespace changes}
    \end{itemize}
  \end{column}
  \end{columns}

\end{frame}
