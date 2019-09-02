Some tips for writing code for code coverage tests
==================================================

In Python, the syntax allows for writing code where it can be difficult to
see if the tests coverage for branches and exceptions is high, because the
syntax can flag a line as touched by the tests, even if one of the branches
is unaffected.  We can look at some examples::

    def sample(x):
        return 42 if x > 10 else 43

In this example, you won't be able to see if both of the branches are touched
in the tests, or just one of them. This is obviously not ideal.

For exceptions of the same type, if you write tests that don't test for the
message, it'll be difficult to see which of the exceptions that were raised.
Let's look at some examples::

    import unittest

    def tip(percentage=0):
        if percentage < 0:
            raise ValueError("That's not how tipping works, don't take money from the jar!")
        if percentage == 0:
            raise ValueError("You're not very generous!")
        else if 10 <= percentage <= 20:
            raise ValueError("We like you :)")
        raise ValueError("We reserve a table for you, every day!")

    class Tests(unittest.TestCase):

        def test_too_low(self):
            with self.assertRaises(ValueError):
                tip(percentage=-10)

        def test_better_low(self):
            with self.assertRaises(ValueError) as cm:
                tip(percentage=0)
            self.assertEqual(cm.exception.messages[0], "You're not very generous!")


Similarly to the above examples, collapsing exceptions onto the same ``except``
line will mark the line as touched when you run the test coverage tool. So,
essentially it'll be difficult to see if you have dead code. If you write them
like this, you can see the tests touch all of them::

    try:
        ...
    except ValueError:
        pass
    except KeyError:
        pass

Later, you may consider grouping them together on a single line.

When you run the test coverage tool, lines are marked as either red, green or
yellow depending on their status in the test suites. If you have that iterate
over something, and it doesn't finish
