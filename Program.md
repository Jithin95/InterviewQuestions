### What is the output of this code:

    def extendList(val, list=[]):
        list.append(val)
        return list

    list1 = extendList(10)
    list2 = extendList(123,[])
    list3 = extendList('a')

    print "list1 = %s" % list1
    print "list2 = %s" % list2
    print "list3 = %s" % list3

#### list1 = [10, 'a']
#### list2 = [123]
#### list3 = [10, 'a']

### What is the output of this code:

    def multipliers():
        return [lambda x : i * x for i in range(4)]

    print [m(2) for m in multipliers()]

#### [6, 6, 6, 6]

The reason for this is that Python’s closures are late binding. This means that the values of variables used in closures are looked up at the time the inner function is called. So as a result, when any of the functions returned by multipliers() are called, the value of i is looked up in the surrounding scope at that time. By then, regardless of which of the returned functions is called, the for loop has completed and i is left with its final value of 3.

One solution would be use a Python generator as follows:

    def multipliers():
        for i in range(4): yield lambda x : i * x

Another solution is to create a closure that binds immediately to its arguments by using a default argument.

    def multipliers():
        return [lambda x, i=i : i * x for i in range(4)]

Finally, the easiest fix may be to simply replace the return value’s [] with ()

    def multipliers():
        return (lambda x : i * x for i in range(4))

### What is the output of the code

    class Parent(object):
        x = 1

    class Child1(Parent):
        pass

    class Child2(Parent):
        pass

    print Parent.x, Child1.x, Child2.x
    Child1.x = 2
    print Parent.x, Child1.x, Child2.x
    Parent.x = 3
    print Parent.x, Child1.x, Child2.x

#### Output

    1 1 1
    1 2 1
    3 2 3

The key to the answer is that, in Python, class variables are internally handled as dictionaries. If a variable name is not found in the dictionary of the current class, the class hierarchy (i.e., its parent classes) are searched until the referenced variable name is found (if the referenced variable name is not found in the class itself or anywhere in its hierarchy, an AttributeError occurs).

Therefore, setting x = 1 in the Parent class makes the class variable x (with a value of 1) referenceable in that class and any of its children. That’s why the first print statement outputs 1 1 1.

Subsequently, if any of its child classes overrides that value (for example, when we execute the statement Child1.x = 2), then the value is changed in that child only. That’s why the second print statement outputs 1 2 1.

Finally, if the value is then changed in the Parent (for example, when we execute the statement Parent.x = 3), that change is reflected also by any children that have not yet overridden the value (which in this case would be Child2). That’s why the third print statement outputs 3 2 3.

### What will be the output

    def div1(x,y):
        print "%s/%s = %s" % (x, y, x/y)

    def div2(x,y):
        print "%s//%s = %s" % (x, y, x//y)

    div1(5,2)
    div1(5.,2)
    div2(5,2)
    div2(5.,2.)

In Python 2

    5/2 = 2
    5.0/2 = 2.5
    5//2 = 2
    5.0//2.0 = 2.0

In Python 3

    5/2 = 2.5 # it does not perform integer arithmetic if both operands are integers.
    5.0/2 = 2.5
    5//2 = 2
    5.0//2.0 = 2.0

“double-slash” (//) operator will always perform integer division, regardless of the operand types

### What will be the Output

    list = ['a', 'b', 'c', 'd', 'e']
    print list[10:]

#### Output
The above code will output [], and will not result in an IndexError.

What makes this a particularly nasty gotcha is that it can lead to bugs that are really hard to track down since no error is raised at runtime.

### WHat will be the Output

    1. list = [ [ ] ] * 5
    2. list  # output?
    3. list[0].append(10)
    4. list  # output?
    5. list[1].append(20)
    6. list  # output?
    7. list.append(30)
    8. list  # output?

#### Output

    [[], [], [], [], []]
    [[10], [10], [10], [10], [10]]
    [[10, 20], [10, 20], [10, 20], [10, 20], [10, 20]]
    [[10, 20], [10, 20], [10, 20], [10, 20], [10, 20], 30]

list[0].append(10) appends 10 to the first list. But since all 5 lists refer to the same list, the output is:

    [[10], [10], [10], [10], [10]].

Similarly, list[1].append(20) appends 20 to the second list. But again, since all 5 lists refer to the same list, the output is now:    

    [[10, 20], [10, 20], [10, 20], [10, 20], [10, 20]].

In contrast, list.append(30) is appending an entirely new element to the “outer” list, which therefore yields the output:

    [[10, 20], [10, 20], [10, 20], [10, 20], [10, 20], 30].
