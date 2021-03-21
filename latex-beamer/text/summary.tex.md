%!TEX TS-program = ../make.zsh

\begin{frame}{Summary}
  \begin{itemize}
    \item Thoughtful \alert{merging} can \alert{save time} in the long run and helps keeping a \alert{long-term track of the context} where changes came from
    \item Bring two equivalent git graph nodes \alert{(commits) $A$ and $B$ together} with: \\
      `git co A` \\
      `git merge --allow-unrelated-histories --strategy=ours` \\
      `  --no-ff -X ignore-all-space B`
    \item Use `strategy=ours` vs. `strategy=theirs` careful depending on which branch should be followed in `git blame`
    \item Be careful not to rename the `main` branch
    \item Be sure to go through a \alert{pull request} to bring it into `main` in order to double-check that everything has worked as intended
  \end{itemize}
\end{frame}
