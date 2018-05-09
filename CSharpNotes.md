# C# Notes

## 1. Collections
### IEnumerable - A user defined collection type that implements the IEnumerable interface - specifically implementing the GetEnumerator method.  

```cs
    public class Garage : IEnumerable
    {
        private Car[] _cars;

        public Garage()
        {
            _cars = new Car[4];
            _cars[0] = new Car("Rusty", 30);
            _cars[1] = new Car("Clunker", 55);
            _cars[2] = new Car("Zippy", 30);
            _cars[3] = new Car("Fred", 30);
        }

        public IEnumerator GetEnumerator() => _cars.GetEnumerator();
    }
```
Given that the GetEnumerator method has been defined publicly, a client of this class could iterate the collection by interacting directly with the IEnumerator type as follows:

```cs
    class Program
    {
        static void Main(string[] args)
        {
            var garage = new Garage();
            var i = garage.GetEnumerator();
            while (i.MoveNext())
            {
                var c = (Car)i.Current;
                Console.WriteLine("{0} is going {1} MPH", c.PetName, c.CurrentSpeed);
            }
            Console.ReadLine();
        }
    }
```
If you prefer to hide the functionality of IEnumerable from the client of this collection class, then make use of explicit interface implementation. By doing so, any client would be forced to iterate the collection using the more terse foreach as follows:

```cs
    public class Garage : IEnumerable
    {
        ...
        IEnumerator IEnumerable.GetEnumerator() => _cars.GetEnumerator();
    }

    class Program
    {
        static void Main(string[] args)
        {
            var garage = new Garage();
            foreach (Car c in garage)
            {
                Console.WriteLine("{0} is going {1} MPH", c.PetName, c.CurrentSpeed);
            }
            Console.ReadLine();
        }
    }
```
The casual object user will not find the Garage class GetEnumerator() method, while the foreach construct will obtain the interface in the background when necessary.

### Iterator - introduced in C# 2.0, an iterator provides an alternative way to build collection types that work with the foreach loop. 

An iterator is a method, get accessor or operator that enables you to support foreach iteration in a class or struct without having to implement the entire IEnumerable interface. Instead, you provide just an iterator, which simply traverses the data structures in your class. When the compiler detects your iterator, it will automatically generate the Current, MoveNext and Dispose methods of the IEnumerable or IEnumerable interface (i.e. the methods required by the return type of GetEnumerator()).

"Whereas a foreach statement is the consumer of the enumerator, an iterator is the producer of the enumerator."

The above is how "C# 5.0 In A NutShell" explains it, and has been helpful for me.

In other words, the foreach statement uses MoveNext(), and the Current property of the IEnumerator to iterate through a sequence, while the iterator is used to produce the implementation of the IEnumerator that will be used by the foreach statement. In C#, when you write an iterator method containing a yield statement, the compiler will generate a private enumerator for you. And when you iterate through the items in the sequence, it will call the MoveNext() and Current property of the private enumerator. These methods/properties are implemented by your code in the iterator method that will be called repeately to yield values until there are not values left to yield. 