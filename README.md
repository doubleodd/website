# Website Source

To generate the Web site itself, use
[mdBook](https://rust-lang.github.io/mdBook/). This is a simple matter
of installing the `mdbook` command (compiling from source to get the
binary is easy) and running `mdbook build`. This will create and
populate the `book` directory with all the files.

For publication, the output files will be copied into the
`doubleodd.github.io` repository.
