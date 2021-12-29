# solid-principle-


Single Responsibility Principle
This states that a class should have a single responsibility, but more than that, a class should only have one reason to change.

Taking an example of the (simple) class called Page.


import json

class Page():
    def __init__(self, title):
        self._title = title

    def get_title(self):
        return self._title

    def set_title(self, title):
        self._title = title

    def get_page(self):
        return [self._title]

    def format_json(self):
        return json.dumps(self.get_page())
        
        
        
import json

class Page():
    def __init__(self, title):
        self._title = title

    def get_title(self):
        return self._title

    def set_title(self, title):
        self._title = title

    def get_page(self):
        return [self._title]

class JsonPageFormatter():
    def format_json(page: Page):
        return json.dumps(page.get_page())
        
        
Open/Closed Principle
In the open/closed principle classes should be open for extension, but closed for modification. Essentially meaning that classes should be extended to change functionality, rather than being altered into something else.


class Rectangle():
    def __init__(self, width, height):
        self._width = width
        self._height = height

    def get_width(self):
        return self._width

    def set_width(self, width):
        self._width = width

    def get_height(self):
        return self._height

    def set_height(self, height):
        self._height = height

class Board():
    @property
    def rectangles(self):
        return self._rectangles

    @rectangles.setter
    def rectangles(self, value):
        self._rectangles = value  

    def calculateArea(self):
        area = 0
        for item in self.rectangles:
            area += item.get_height() * item.get_width()
        return area
        
 
import math

class ShapeMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'area') and callable(subclass.area))

class ShapeInterface(metaclass=ShapeMeta):
    pass

class Rectangle(ShapeInterface):
    def __init__(self, width, height):
        self._width = width
        self._height = height

    def get_width(self):
        return self._width

    def set_width(self, width):
        self._width = width

    def get_height(self):
        return self._height

    def set_height(self, height):
        self._height = height

    def area(self):
        return self.get_width() * self.get_height()

class Circle(ShapeInterface):
    def __init__(self, radius):
        self._radius = radius

    def get_radius(self):
        return self._radius

    def set_radius(self, radius):
        self._radius = radius

    def area(self):
        return self.get_radius() * self.get_radius() * math.pi


The Board class can now be reworked so that it doesn't care what type of shape is passed to it, as long as they implement the area() method.

class Board():
    def __init__(self, shapes):
        self._shapes = shapes

    def calculateArea(self):
        area = 0
        for shape in self._shapes:
            area += shape.area()
        return area
        
        
  Liskov Substitution Principle
Created by Barbara Liskov in a 1987, this states that objects should be replaceable by their subtypes without altering how the program works. In other words, derived classes must be substitutable for their base classes without causing errors.


class Rectangle():
    def __init__(self, width, height):
        self._width = width
        self._height = height

    def get_width(self):
        return self._width

    def set_width(self, width):
        self._width = width

    def get_height(self):
        return self._height

    def set_height(self, height):
        self._height = height

    def area(self):
        return self.get_width() * self.get_height()




class Square(Rectangle):
    def __init__(self, width):
        self._width = width
        self._height = width

    def get_width(self):
        return self._width

    def set_width(self, width):
        self._width = width
        self._height = width

    def get_height(self):
        return self._height

    def set_height(self, height):
        self._height = height
        self._width = height
        
        
        
        
 class QuadrilateralMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'area') and callable(subclass.area)) \
          and (hasattr(subclass, 'get_height') and callable(subclass.get_height)) \
          and (hasattr(subclass, 'get_width') and callable(subclass.get_width)) \

class QuadrilateralInterface(metaclass=QuadrilateralMeta):
    pass

class Rectangle(QuadrilateralInterface):
    pass

class Square(QuadrilateralInterface):
    pass
    
    
    
    
    
    
Interface Segregation Principle
This states that many client-specific interfaces are better than one general-purpose interface. In other words, classes should not be forced to implement interfaces they do not use.


class WorkerMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'take_break') and callable(subclass.take_break)) \
          and (hasattr(subclass, 'write_code') and callable(subclass.write_code)) \
          and (hasattr(subclass, 'call_client') and callable(subclass.call_client)) \
          and (hasattr(subclass, 'get_paid') and callable(subclass.get_paid))

class WorkerInterface(metaclass=WorkerMeta):
    pass 
 
For example, if we create a Manager class then we are forced to implement a write_code() method because that's what the interface requires. Because managers generally don't code we can't actually do anything in this method so we just return false.

class Manager(WorkerInterface):
    def write_code(self):
        pass
        
Also, if we have a Developer class that implements Worker then we are forced to implement a call_client() method because that's what the interface requires.

class Developer(WorkerInterface):
    def call_client(self):
        pass
        
        
        
Having a fat and bloated interface means having to implement methods that do nothing.

The correct solution to this is to split our interfaces into separate parts, each of which deals with specific functionality. Here, we split out a Coder and ClientFacer interface from our generic Worker interface.

class WorkerMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'take_break') and callable(subclass.take_break)) \
          and (hasattr(subclass, 'get_paid') and callable(subclass.get_paid))

class WorkerInterface(metaclass=WorkerMeta):
    pass


class ClientFacerMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'call_client') and callable(subclass.call_client))

class ClientFacerInterface(metaclass=ClientFacerMeta):
    pass


class CoderMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'write_code') and callable(subclass.write_code))

class CoderInterface(metaclass=CoderMeta):
    pass
    
    
With this in place we can implement our sub-classes without having to write code that we don't need. So our Developer and Manager classes would look like this.



class Manager(WorkerInterface, ClientFacerInterface):
    pass

class Developer(WorkerInterface, CoderInterface):
    pass
        
        
        
        
Dependency Inversion Principle
Perhaps the simplest of the principles, this states that classes should depend upon abstractions, not concretions. Essentially, don't depend on concrete classes, depend upon interfaces.


class MySqlConnection():
    def connect(self):
        pass

class PageLoader():
    def __init__(self, mysql_connection: MySqlConnection):
        self._mysql_connection = mysql_connection
        
        
    
    
  class DbConnectionMeta(type):
    def __instancecheck__(self, instance):
        return self.__subclasscheck__(type(instance))

    def __subclasscheck__(self, subclass):
        return (hasattr(subclass, 'connect') and callable(subclass.connect))

class DbConnectionInterface(metaclass=DbConnectionMeta):
    pass


class MySqlConnection(DbConnectionInterface):
    def connect(self):
        pass

class PageLoader():
    def __init__(self, db_connection: DbConnectionInterface):
        self._db_connection = db_connection
