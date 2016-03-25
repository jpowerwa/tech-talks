# Write Better Python: Unit Tests

Joanna Power  
March 2016  
[Presentation slides](better-python_unit-tests_5min.pdf)


### Intro

  The only way not to write bugs is not to code.  
  Testing finds bugs.  
  Testing is part of writing good code.  


### What are unit tests?
Programmatic verification of your code, bit by bit.

  Unit tests are code that verify other code.  
  Unit tests can verify calculations, conditional control flow, call paths, side effects.  
  They are also great for verifying exception handling, which is otherwise hard to test.  
  Each test should verify one method's handling of a single scenario.  

  White box testing  
  Feel free to muck with the internals of your objects before your test verifies a particular scenario.   
  This is necessary in order to keep unit tests properly scoped.  


### What aren't unit tests?
Unit tests verify neither end-to-end behavior nor behavior in production.

  Unit tests are not integration tests, load tests, or performance tests.  
  They do not verify user scenarios.  
  They do not verify your environment or configuration.  
  They should not rely on persisted data.  
  They should leave no trace.  


### Why should I write unit tests?

**Short term**  
  Ship fewer bugs  
  Protect against regressions  

**Longer term**  
  Maintainable code  

**Others**  
  Resource for new developer  
  Promote developer accountability: If the tests worked after you made your changes and break later, it wasn't because of your changes.  
  Opportunity to lead by example and influence best practices.  


### How do I write unit tests?

```python

    import unittest

    class MyTests(unittest.TestCase):

      def test_sanity__true(self):
        self.assertTrue(True)

      def test_sanity__none(self):
        self.assertIsNone(None)

    if __name__ == '__main__':
        unittest.main()
```

Python makes unit testing easy.  
  Framework is built into python.  
  Inherit from `unittest.TestCase`.  
  Methods with names that start with `test_` run automatically.  
  Each test will pass, fail, or raise an uncaught exception.  
  A test verifies correctness or functionality using assert methods: assertTrue, assertIsNone, etc.  
  If any assert fails, the individual test immediately fails.  
  Run tests by calling `unittest.main()`.  
  You can also run tests from the command line.  


### More realistic example

```python

    class Person(object):
      def __init__(self, name=None):
        if not self.check_name(name):
          raise ValueError('Invalid name')
        self.name = name
   
      def check_name(self, name):
        return (
          name 
          and name != 'Hulk Hogan' 
          and len(name.split()) > 1)
```


### Mocking

```python

    import unittest
    import unittest.mock

    class PersonTests(unittest.TestCase):
        @mock.patch.object(Person, 'check_name')
        def test_init(self, mock_check_name):
        '''Verify result and calls made by __init__
        '''
        # Set up mocks and test data
        mock_check_name.return_value = True
        mock_name = mock.Mock(name='mock_name')

        # Make call
        person = Person(name=mock_name)
    
        # Verify result
        self.assertEqual(mock_name, person.name)
    
        # Verify mocks
        mock_check_name.assert_called_once_with(mock_name)
```
    
  Notice the new import: `unittest.mock`  
  In python 2, install mock and import as `mock`  

  Q: What is mock?  
  A mock is something that is substituted for something else.  

  Q: What is the value of using mocks?  
  Mocks are necessary to create the isolation required for a unit test.  
  Your code is part of a complex system.   
  Mocks allow you to hide that complexity.  

  It's like a thought experiment: "Hypothetically speaking..."  
  Simplify everything except the actual question you are trying to answer.  

You can mock methods, objects, classes.  
  We mock `Person.check_name` method to return True. The real `Person.check_name` method will not be called.  
  We mock the name object to hide the detail that name is a string. Maybe it will be an object later. The `__init__` method doesn't need to know or care.

Mocked methods keep track of their calls: `assert_called_once_with`  

Q: Why bother mocking a simple method like `check_name`?  
A: This is a test for `__init__`, not `check_name`; `check_name` gets its own tests.  
The number of unit tests for a method should correspond to the number of scenarios that method handles.  
Two scenarios for `__init__`:  
1. `check_name` returns True and constructor succeeds; internal name variable is set. This is the test you see.  
2. `check_name` returns False and constructor raises exception. This test is not written in the example.  


### Recap

A unit test verifies a small bit of functionality.  
Unit tests lead to better code.  
Python makes writing and running unit tests easy.  
Unit tests without mocking are not unit tests.  


### Recommended resources

<https://cgoldberg.github.io/python-unittest-tutorial/>  
<https://docs.python.org/3/library/unittest.mock-examples.html>  


