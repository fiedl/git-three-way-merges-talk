%!TEX TS-program = ../make.zsh

\newcommand\done{\checkmark\xspace}
\newcommand\inprogress{$\Rightarrow$\xspace}
\newcommand\tobedone{$\square$\xspace}

\newcommand\question[1]{\colorbox{blue!10}{\fbox{\ \ \questionicon \ \ #1}}}

\begin{frame}[fragile]{Overview of the review}

  \begin{itemize}
    \item State of the review:
      \begin{itemize}
        \item[\done] First glance: Documentation, example scripts, unit tests, build
        \item[\done] Directory structure
        \item[\inprogress] Build, run, and review tests
        \item[\inprogress] Review code structure and readability
        \item[\tobedone] Review documentation
        \item[\tobedone] Review coding standards
        \item[\tobedone] Review usability
      \end{itemize}

      \ \ \ \tiny See also: \url{http://software.icecube.wisc.edu/documentation/general/code_reviews.html} \normalsize

    \item Current state documented in \githubicon \url{https://github.com/fiedl/monopole-generator/issues/1}.

      Results will be published to \href{https://github.com/fiedl/monopole-generator/tree/master/resources/docs}{`resources/docs`} after completing the review.

    \item This talk: **Tests**
  \end{itemize}

\end{frame}

\begin{frame}[fragile]{How to run the tests}
  \begin{itemize}

    \item There are **python tests** in \href{https://github.com/fiedl/monopole-generator/tree/f68f19ff7a4d8fead45b4d6ae345723da69edbf6/resources/examples}{`resources/test`} and **cxx tests** in \href{https://github.com/fiedl/monopole-generator/tree/f68f19ff7a4d8fead45b4d6ae345723da69edbf6/private/test}{`private/test`} as expected.

    \item How to run the **python tests**:
      \begin{bash}
        [2019-10-29 23:16:56] fiedl@fiedl-mbp ~/icecube/software/icecube-combo-stable-2019-10-29/debug_build
        ▶ ./env-shell.sh python ../src/monopole-generator/resources/test/test_monopole_generator.py
        ▶ ./env-shell.sh python ../src/monopole-generator/resources/test/test_monopole_propagator.py
      \end{bash}

    \item How to run the **cxx tests**:
      \begin{bash}
        [2019-10-29 23:16:56] fiedl@fiedl-mbp ~/icecube/software/icecube-combo-stable-2019-10-29/debug_build
        ▶ make test-bins
        ▶ ./env-shell.sh bin/monopole-generator-test --all
      \end{bash}

    \item There is also a `make monopole-generator-test` in the `monopole-generator/Makefile`.

      \begin{bash}
        [2019-10-30 00:08:42] fiedl@fiedl-mbp ~/icecube/software/icecube-combo-stable-2019-10-29/debug_build/monopole-generator
        ▶ ../env-shell.sh make monopole-generator-test
      \end{bash}

      \question{Is this the same as in `make test-bins` from the root directory?}

  \end{itemize}
\end{frame}

\begin{frame}[fragile]{Running the tests \& test results}
  \begin{itemize}

    \item **Last time**: Tests did not run due to a **missing `./env-shell.sh`** \\
      \url{https://github.com/fiedl/monopole-generator/issues/2}

    \item Now: **Python tests** do run and **succeed**.

      \begin{bash}
        [2019-10-29 21:21:04] fiedl@fiedl-mbp ~/icecube/software/icecube-simulation-V06-01-01
        ▶ debug_build/env-shell.sh python src/monopole-generator/resources/test/test_monopole_generator.py
        ----------------------------------------------------------------------
        Ran 40 tests in 159.557s
        OK

        [2019-10-29 21:24:00] fiedl@fiedl-mbp ~/icecube/software/icecube-simulation-V06-01-01
        ▶ debug_build/env-shell.sh python src/monopole-generator/resources/test/test_monopole_propagator.py
        ----------------------------------------------------------------------
        Ran 4 tests in 0.014s
        OK
      \end{bash}

    \item **Cxx tests** do **run**, but **some fail**.

      \begin{bash}
        [2019-10-29 21:18:46] fiedl@fiedl-mbp ~/icecube/software/icecube-simulation-V06-01-01/debug_build
        ▶ ./env-shell.sh bin/monopole-generator-test --all
        ===================================================================
        Pass: 10
        Fail: 4
         THESE TESTS FAIL
            SlowMonopoleTest.cxx/DefaultSecondariesTest
            SlowMonopoleTest.cxx/GeneralSecondariesTest
            SlowMonopoleTest.cxx/TestMFP
            SlowMonopoleTest.cxx/TestScaling
      \end{bash}

  \end{itemize}
\end{frame}

\begin{frame}[fragile]{Why do tests fail?}
  \begin{itemize}
    \item Failure message:

      \begin{bash}
        FATAL (I3MonopoleGenerator): Requested less than 1 event to be generated. Abort! (I3MonopoleGenerator.cxx:92 in virtual void I3MonopoleGenerator::Configure())
      \end{bash}

    \item There are consistency checks in the code:

      \begin{cpp}
        // src/monopole-generator/private/monopole-generator/I3MonopoleGenerator.cxx
        GetParameter("NEvents", Nevents_);
        GetParameter("TreeName", treeName_);
        GetParameter("Mass", mass_);
        // ...
        if (Nevents_<= 0){
          log_fatal("Requested less than 1 event to be generated. Abort!");
        }
      \end{cpp}

    \item Maybe the consistency checks were not present when the tests were created.
    \item Not sure why the CI did not complain when introducing the consistency checks.

  \end{itemize}
\end{frame}

\begin{frame}[fragile]{Fixing the tests}
  \begin{itemize}

    \item Setting `NEvents` in the test fixes the issue, but then the test runs in the next consistency check.
    \item[\inprogress] Need to fix the tests by adding the minimum of required parameters.

    \item \question{Should I fix the tests?}
    \item \question{When should the tests be fixed? During the review or afterwards?}
    \item \question{Where should the tests be fixed? What's our workflow?}

      \small I would fix them in a branch on \githubicon \url{https://github.com/fiedl/monopole-generator}. Should I mirror the fixes to a feature branch on the svn? What's our svn workflow?
  \end{itemize}
\end{frame}


\begin{frame}[fragile]{Testing against combo}
  \begin{itemize}

    \item On Monday, I've learned that \href{http://code.icecube.wisc.edu/svn/meta-projects/simulation}{icecube-simulation} is discontinued and everyone should switch to \href{http://code.icecube.wisc.edu/svn/meta-projects/combo}{icecube-combo}. \inprogress Better test against combo rather than simulation.

    \item \question{What is Combo? Where can I find documentation?}

    \item \href{http://code.icecube.wisc.edu/svn/meta-projects/combo}{icecube-combo} has a \href{http://code.icecube.wisc.edu/svn/meta-projects/combo/trunk}{trunk}, a \href{http://code.icecube.wisc.edu/svn/meta-projects/combo/stable}{stable}, \href{http://code.icecube.wisc.edu/svn/meta-projects/combo/candidates}{candidates}, and \href{http://code.icecube.wisc.edu/svn/meta-projects/combo/releases}{releases}.

    \begin{itemize}
      \item \question{What is `stable`?} Is it pointing to the latest release or is it just `trunk` as soon as the tests are passing?
      \item \question{How often are there new releases? How long are releases supported?} By \textit{supported} I mean that they are supposed to work when installing on a fresh system, i.e. they get patches when something upstream (boost, \dots) would break them.
      \item \question{Is there already CI in place for `trunk`, `stable` and `candidates`?} I wasen't sure on \url{http://builds.icecube.wisc.edu}.
      \item \question{How can I find the version numbers of projects included within a combo release?} For example: The version of \textit{clsim} that is included in `icecube-combo-V00-00-00-RC2`? From the `src/clsim/RELEASE_NOTES` it looks like there is code from the clsim `trunk` inside `icecube-combo-V00-00-00-RC2` that has not been inside a clsim release.
    \end{itemize}

  \end{itemize}
\end{frame}


\begin{frame}[fragile]{Testing against combo with github actions}
  \begin{itemize}

    \item \href{https://github.com/features/actions}{Github actions} will be released on 13 November 2019.
    \item With a beta account, I've already setup:

    \begin{itemize}
      \item \url{https://github.com/fiedl/icecube-combo-install} --- builds icecube-combo on ubuntu and macOS
      \item \url{https://github.com/fiedl/monopole-generator-install} --- builds and tests the monopole generator against combo.
    \end{itemize}

    \item \question{Can I somehow use webhooks} to trigger a build when something is pushed to the `trunk` or the `stable`  on the svn?

  \end{itemize}
\end{frame}


\begin{frame}[fragile]{CI results with github actions}
  \begin{itemize}

    \item Building icecube-combo
      \begin{tabularx}{\textwidth}{r||c|c|c}
          & trunk
          & stable
          & V00-00-00-RC2 \\ \hline\hline
        macOS Mojave
          & \href{https://github.com/fiedl/icecube-combo-install/runs/280059867}{build: \done}
          & \href{https://github.com/fiedl/icecube-combo-install/runs/280059833}{build: \done}
          & \href{https://github.com/fiedl/icecube-combo-install/runs/280059801#step:3:0}{build: failed} \\
        ubuntu 18.04
          & \href{https://github.com/fiedl/icecube-combo-install/runs/280059878}{build: \done}
          & \href{https://github.com/fiedl/icecube-combo-install/runs/280059852}{build: \done}
          & \href{https://github.com/fiedl/icecube-combo-install/runs/280059816#step:3:0}{build: failed} \\
      \end{tabularx}

      \begin{onlyenv}<1>
        How does it fail?

        \begin{bash}
          # ubuntu
          /usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu/libboost_python.so: undefined reference to 'PyString_Size'
          collect2: error: ld returned 1 exit status
          make[2]:  [bin/clsim-make_safeprimes] Error 1
          make[1]:  [clsim/CMakeFiles/clsim-make_safeprimes.dir/all] Error 2

          # macOS
          error: 'dispose' is missing exception specification 'noexcept'
        \end{bash}
      \end{onlyenv}

    \item Biilding `monopole-generator` and `test-bins` against combo

      \begin{onlyenv}<2->
        \begin{tabularx}{\textwidth}{r||c|c|c}
            & trunk
            & stable
            & V00-00-00-RC2 \\ \hline\hline
          macOS Mojave
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536260}{build: \done}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536223}{build: \done}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536194}{?} \\
          ubuntu 18.04
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536241#step:4:0}{build: failed}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536210#step:4:0}{build: failed}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536177}{?} \\
        \end{tabularx}
      \end{onlyenv}
      \begin{onlyenv}<2>
        How does it fail?

        \begin{bash}
          Python version mismatch found:
          IceTray was compiled with 3.6.8
          Currently running with    2.7.15+

          Environment not (re)loaded.
          [error]Process completed with exit code 2.
        \end{bash}

        Probably did something wrong with `env-shell.sh` again. But does work locally.
      \end{onlyenv}

    \item Monopole-generator python tests
      \begin{onlyenv}<3->
        \begin{tabularx}{\textwidth}{r||c|c|c}
            & trunk
            & stable
            & V00-00-00-RC2 \\ \hline\hline
          macOS Mojave
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536260#step:6:0}{build: \done}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536223#step:6:0}{build: \done}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536194}{?} \\
          ubuntu 18.04
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536241}{?}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536210}{?}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536177}{?} \\
        \end{tabularx}
      \end{onlyenv}

    \item Monopole-generator cxx tests
      \begin{onlyenv}<4->
        \begin{tabularx}{\textwidth}{r||c|c|c}
            & trunk
            & stable
            & V00-00-00-RC2 \\ \hline\hline
          macOS Mojave
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536260#step:7:0}{build: 4 fail}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536223#step:7:0}{build: 4 fail}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536194}{?} \\
          ubuntu 18.04
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536241}{?}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536210}{?}
            & \href{https://github.com/fiedl/monopole-generator-install/runs/280536177}{?} \\
        \end{tabularx}
      \end{onlyenv}

  \end{itemize}
\end{frame}

\begin{frame}[fragile]{CI results with github actions}
  \begin{itemize}

    \item There are warnings like \textit{'Some parts of test disabled until I figure out what the actual avalues here should be'} in the tests. \inprogress I'll ignore them for now, until I'm familiar with the tool.

  \end{itemize}
\end{frame}


\begin{frame}[fragile]{Repository migration}
  \begin{itemize}

    \item Currently the source code lives in: \\
      \url{http://code.icecube.wisc.edu/svn/projects/monopole-generator} \\
      \url{https://github.com/fiedl/monopole-generator}

    \item \question{What's the migration plan} to get it to \url{https://github.com/icecubeopensource/monopole-generator}?

    \item \question{What about \url{https://github.com/icecube}?} Can't we get this?

  \end{itemize}
\end{frame}