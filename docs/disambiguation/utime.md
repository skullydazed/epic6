# Utime

Do you mean:

- The built-in [utime function](../functions/utime.md), `$utime()`, which returns the current client-host wall-clock timestamp as two words: epoch seconds and microseconds within that second?
- The [utime script](../scripts/utime.md), loaded with `/load utime`, which defines `utime_add`, `utime_sub`, and `time_since` for arithmetic on those pairs?

The script does not replace the built-in function. Its helpers also return two words, not a decimal number. The built-in microsecond field is unpadded, so simply placing a decimal point between its fields gives the wrong fractional value unless you first pad it to six digits.

These use local time readings, not an IRC server query. Wall-clock adjustments can move readings backward or forward. See the script page for normalization limits and the exact-one-million-microseconds addition bug before relying on its arithmetic.

For whole epoch seconds only, see [time](../functions/time.md); for readable formatting, see [strftime](../functions/strftime.md).

## Source references

- `source/functions.c`: built-in timestamp function.
- `source/ircaux.c`: underlying time helper.
- `script/utime`: pair arithmetic aliases.
