>In class-based programming, the factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.

Instead of explicitly using constructors for the various subtypes, we use an implementation of the factory method. The factory is implemented using c++11 variadic templates. It holds a map of creation functions for each object in the inheritance hierarchy:
```cpp
typedef std::function<std::unique_ptr<Animal>(const std::string &)> CreateAnimal;
```
It dispatches them based on a preassigned tag, so that creation of objects will look like the following:
```cpp
std::unique_ptr<Animal> simon = farm.create("CAT", "Simon");
std::unique_ptr<Animal> marlowe = farm.create("Dog", "Marlowe");
```

The tags and creation functions are registered to the factory prior to its use:
```cpp
Factory<CreateAnimal> farm;
farm.subscribe("CAT", [](const std::string & n) {return std::make_unique<Cat>(n);});
farm.subscribe("DOG", [](const std::string & n) {return std::make_unique<Dog>(n);});
```

what should the Factory function return? It could certainly return a raw pointer, Widget\*. After all, that is what new returns. But this opens the way to incorrect use of the WidgetFactory—for example, instead of capturing the returned raw pointer in a unique pointer, we could pass it to a function such as Transmogrify that takes a raw pointer because it does not deal with the ownership. Now, nobody owns the widget, and it ends up as a memory leak. Ideally, WidgetFactory would be written in a way that would force the caller to take ownership of the returned object.

