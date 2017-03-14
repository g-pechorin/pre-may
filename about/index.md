
I'm Peter.

I like to program things.



## Publications
### [Aspects of Development of Computer Games for Therapeutic Purposes](http://link.springer.com/chapter/10.1007/978-3-319-23766-4_59#page-1)
```
@incollection{blanchfield2015aspects,
  title={Aspects of Development of Computer Games for Therapeutic Purposes},
  author={Blanchfield, Peter and La Valle, Peter and Nguyen, Tuan and Bradley, Matthew and Liu, Dan},
  booktitle={Creativity in Intelligent Technologies and Data Science},
  pages={748--754},
  year={2015},
  publisher={Springer}
}
```

## Contributions

I don't live in a bubble, but I do have a rubbish memory.
I remain surprised when I'm credited as a contributor to a FaOS project which I'm using.
Listed in alphabetical order are contributions I've made to other people's work.
Most are a very-very small number of changes which took more-than-a-little poking around to discern what was wrong and how to fix it.

* [assimp](https://github.com/assimp/assimp/commits?author=g-pechorin) is a (universal? game?) asset translator/importer
  * I submitted some spelling corrections to XML tags. Without the corrections, the XML tags crashed strict XML importers
  * Adjustments to the build system. Let it work on computers *without* zlib or git installations
  * I corrected some documentation corrections for the binary-format; places where the commenting didn't reflect the source
* [stb_ttf](https://github.com/nothings/stb/commit/63550ae9edefffece9b4b50cdb47353b5d9ee534) is a single-header TTF library
  * fixed an actual, honest to goodness bug!
* [TinyWindow](https://github.com/ziacko/TinyWindow/commits?author=g-pechorin) is a single-header-library to create and manage an OpenGL window
  * I found and resolved a chicken-or-egg bug with smart-pointers and resource deallocation
  * I submitted some bug fixes, then I went back to GLFW
