---
layout: default
---

Today, I found some codes was written a year ago, that's written for my own ORM implementations. The surprising feature is set
derived class properties from base class, this feature always be used by some software architect. The simplify code (demo) as following:

```csharp 
    public class A {
        public A() {
            var derivedProperties = this.GetType().GetProperties();
            foreach (var prop in derivedProperties) {
                var type = prop.PropertyType;
                if (type.IsGenericType && type.GetGenericTypeDefinition()
                        == typeof(IIter<>)) {
                    var itemType = type.GetGenericArguments()[0];

                    prop.SetValue(this, Activator.CreateInstance(typeof(InternalIter<>).MakeGenericType(itemType)));
                }
            }
        }
    }

    public interface IIter<T> {
        T Data { get; }
    }

    internal class InternalIter<T> : IIter<T> {
        public T Data { get; set; }
    }

    public class B : A {
        public IIter<string> Data { get; set; }
    }

    [TestClass]
    public class UnitTest1 {
        [TestMethod]
        public void TestMethod1() {
            var b = new B();
            var type = b.Data.GetType();
            Assert.AreEqual(typeof(InternalIter<string>), type);
        }
    }

```