# python: dict preserves insertion order

since python 3.7 dicts keep insertion order. not a language implementation detail anymore — it's in the spec. same with `collections.OrderedDict` — those are basically equivalent now for ordering, though OrderedDict still has a few quirks (equality ignores order in regular dict, respects it in OrderedDict).

tl;dr: you can stop using OrderedDict just for ordering. use it only when you actually need `move_to_end()` or order-sensitive equality.
