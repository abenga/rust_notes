# Iterators

The iterator pattern allows us to perform some tasks on a sequence of items in
turn. An iterator is responsible for the logic of iterating over each item and
determining when the sequence has finished. Iterators are *lazy* -- have no
effect until you call methods that consume the iterator to use it up.