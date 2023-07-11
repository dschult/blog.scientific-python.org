---

title: "May 2023 Developer Summit"
date: 2023-07-09T10:07:40-04:00
draft: false
description: "
Sparse Arrays at the May Developer Summit in Seattle
"
tags: ["summits", "scipy.sparse"]
displayInList: true
author: ["Dan Schult", "Stéfan van der Walt"]
---

## Scientific-Python Developer Summit

(May 22-26, 2023, Seattle WA) --
The Scientific Python Developer Summit provided an opportunity
for core developers from the scientific Python ecosystem to come together to:

1. improve joint infrastructure
2. better coordinate core projects
3. work on a shared strategic plan

Related notes/sites:

- [Worklog](https://hackmd.io/iEtdfbxfSbGwOAJTXmqyIQ?view).
- [Planning Meeting Notes and Info](https://blog.scientific-python.org).

One of the projects in focus at the summit was SciPy Sparse Arrays.
This blog attempts to recap what happened with "sparse" at the summit
and a glimpse of plans for our continuing work. The Sparse Array Group
holds [open follow-up meetings currently scheduled every two weeks](https://scientific-python.org/calendars)
to continue the momentum and move this project forward.

At the Summit, we focused on converting Sparse Matrix capabilities
in Scipy from the Sparse Matrix API to the new Sparse Array API. This
shifts the interface from one like NumPy-Matrix to one like NumPy-Array.
Of course, this work began well before the summit with the introduction
of sparse arrays into the scipy.sparse subpackage.
Our goal in the summit was to give some focused energy to the effort,
bring new people on board and connect downstream users with the development
effort. We also worked to create a community for this project that would
last beyond the summit itself.

The specific PRs and Issues involved in Scipy Sparse are detailed in the
[Summit 2023 scipy.sparse Report](https://hackmd.io/1Q2832LDR_2Uv_-cV-wnYg),
with more detailed description appearing in the
[Summmit Worklog](https://hackmd.io/iEtdfbxfSbGwOAJTXmqyIQ?view).
Some big picture take-aways are:

- We reorganized how to check for matrix/array/format info. This involved
  adding a `format` attribute describing which format of sparse storage is used,
  adding an `_is_array` attribute to assist in converting from the matrix to array
  interfaces, changing functions `issparse`/`isspmatrix` as well as shifting
  the class hierarchy to allow easy `isinstance` checking.
  The interface going forward includes:
  - `issparse(A)`: True when a sprase array or matrix.
  - `isinstance(A, sparray)` or alternatively `issparse(A) and A._is_array`: True when a sprase array.
  - `isspmatrix(A)` or alternatively `issparse() and not A._is_array`: True when a sparse matrix.
    To check the format of a sparse array or matrix use `A.format == "csr"` or similar.
- We updated the `sparray` interface, leaving `spmatrix` untouched for backward compatibility.
- We made decisions about how to approach the "creation functions" for sparse arrays.
  The big-picture approach is to introduce new functions with an `_array` suffix which
  construct sparse arrays. The old names will continue to create sparse matrix until
  post-deprecation removal.
  Some specific changes made include:
  - Add the creation function `diags_array(A)` (and planned for `eye_array`, `random_array` and others).
  - Create a `sparse.linalg.matrix_power` function for positive integer matrix power of a sparse array
- Made progress toward 1D sparse arrays. The data structures for 1d may be quite different from 2d.
  A prototype `coo_array` allowed exploration of possible n-d arrays, though that is not a short-term goal.
- Explored feasibility and usefulness of defining `__array_ufunc__` and other `__array_*__` protocols for sparse arrays
- Improved documentation for sparse arrays

We have set a goal of a working set of sparse array construction functions
and a 1d sparse array class (focusing on `coo_array` first) in plenty of
time for intensive testing before SciPy v1.12. This will then allow us to
focus on creating migration documents and tools as well as helping downstream
libraries make the shift to sparse arrays. We hope to enable the removal of
deprecated sparse matrix interfaces in favor of the array interface. For this
to happen we will need most downstream users to shift to the sparse array api.
We intend to help them do that.

Our work continues with a community call every [two weeks on Fridays.](https://scientific-python.org/calendars)
Our immediate near term work involves:

- Continue improving sparse creation functions: diags, eye, random and others.
- Deprecate some matrix-specific functionality
- General performance improvements
- Adapting scikit-learn to support sparse arrays (to be discussed with scikit-learn's maintainers)

This blog was largely composed based on the [scipy-sparse report hackmd document](https://hackmd.io/1Q2832LDR_2Uv_-cV-wnYg).