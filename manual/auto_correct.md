# Auto-correct

In auto-correct mode, RuboCop will try to automatically fix offenses:

```sh
$ rubocop -a
```

For some offenses, it is not possible to implement automatic correction.

Some automatic corrections that _are_ possible have not been implemented yet.

## Safe auto-correct

```sh
$ rubocop --safe-auto-correct
```

In RuboCop 0.60, we began to annotate cops as `Safe` or not safe. The definition of
safety is that the cop doesn't generate false positives and doesn't suggest changes
that are not equivalent to the original code. On top of that there's `SafeAutoCorrect`
that might be set to `false` in cases where only the auto-correct performed by a cop
is unsafe, but that the offense detection logic is safe. To sum it up:

- Safe (`true/false`) - indicates whether the cop can yield false positives (by
  design) or not.
- SafeAutoCorrect (`true/false`) - indicates whether the auto-correct a cop
  does is safe (equivalent) by design. If a cop is unsafe its auto-correct automatically
  becomes unsafe as well.

If a cop or its auto-correct is annotated as "not safe", it will be omitted when using `--safe-auto-correct`.

!!! Note

    Currently there might still be cops that are marked as unsafe or with unsafe auto-correct.
    Eventually, the safety of each cop will be specified in the default configuration.

### Example of Unsafe Cop

```ruby
array = []
array << 'Foo' <<
         'Bar' <<
         'Baz'
puts array.join('-')
```

`Style/LineEndConcatenation` will correct the above to:

```ruby
array = []
array << 'Foo' \
  'Bar' \
  'Baz'
puts array.join('-')
```

Therefore, in this (unusual) scenario, `Style/LineEndConcatenation` is unsafe.

(This is a contrived example. Real code would use `%w` for an array of string
literals.)

## Generating comments

```sh
$ rubocop --auto-correct --disable-uncorrectable
```

or

```sh
$ rubocop --safe-auto-correct --disable-uncorrectable
```

You can add the flag `--disable-uncorrectable`, which will generate
`# rubocop:todo` comments in the code to stop the reporting of offenses that
could not be corrected automatically.
