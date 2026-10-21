# Perlxcall: removed Perl/array bridge

The former `perlxcall(subroutine [array-in [array-out [index [text]]]])` interface called a subroutine in the embedded Perl interpreter. It could pass EPIC array items as arguments and copy a returned Perl list into an output array. Output-array selection and index validity determined whether the call used list or scalar context; successful list calls returned the number of copied elements.

EPIC6 has removed embedded Perl and no longer registers `perlxcall()`. No bundled replacement provides those calls, output-array mutations, index rules, or result/error conventions. Historical examples that load a Perl subroutine and then call `perlxcall` do not work in the current client.

The simpler [perlcall](perlcall.md) interface is removed too. See [Perl: removed integration](perl.md) and [using embedded Perl: removed](special_using_perl.md) for the migration context. Installing an external Perl interpreter does not restore shared interpreter state or automatic exchange with EPIC arrays.

[Karll arrays](../special/karll_arrays.md), [getitem](../functions/getitem.md), and [setitem](../functions/setitem.md) remain separate client data facilities. A program started through [exec](../commands/exec.md) must exchange data explicitly through subprocess input/output; its Perl arrays are not automatically EPIC arrays.

## Source references

- `source/functions.c`: current registry omits `perlxcall` and related embedded Perl entry points.
- `configure.ac`: current build configuration has no embedded Perl integration.
- `KNOWNBUGS` and `UPDATES`: Perl retirement.
- `source/exec.c`: separate-process integration.
