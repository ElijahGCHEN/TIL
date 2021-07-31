### Difference between `/usr/local` and `/opt`

1. Packages built under `opt` have different directory conventions. They each have their own subdirectory while packages that are installed under `/usr/local` usually follow the conventional directory configuration, i.e. files are distributed into /src, /bin, etc.
2. All files under `/usr` are shareable although this is rarely done with Linux.
3. `/usr/local` usually install packages by the administrator to avoid clashes with packages that are part of the operating system, while `/opt` is entirely managed by users. 