# Student-System
The student system includes functions such as adding students, querying students, modifying students, deleting students, displaying all students, saving data, and exiting. Through this system, student information can be clearly understood

class IStudentStorage(ABC):
    """
    Dependency Inversion Principle (DIP):
    Defines the storage abstraction (Abstraction).
    StudentManager will depend on this interface, not the concrete implementation.
    """

    @abstractmethod
    def load_data(self) -> dict[str, Student]:
        """Loads student data from persistent storage."""
        pass

    @abstractmethod
    def save_data(self, students: dict[str, Student]):
        """Saves student data to persistent storage."""
        pass

        from dataclasses import dataclass

@dataclass
class Student:
    """Defines the Student data model using dataclass."""
    sid: str
    name: str
    age: int
    gender: str  # Male/Female
    major: str

    def __str__(self):
        """Formatted output of student information."""
        return f"ID: {self.sid}\tName: {self.name}\tAge: {self.age}\tGender: {self.gender}\tMajor: {self.major}"

# --- Business Exceptions ---
class StudentError(Exception):
    """Base exception for the Student Management System."""
    pass

class StudentAlreadyExistsError(StudentError):
    """Raised when trying to add a student whose ID already exists."""
    def __init__(self, sid):
        super().__init__(f"Student ID {sid} already exists.")

class StudentNotFoundError(StudentError):
    """Raised when querying/modifying/deleting a non-existent student."""
    def __init__(self, sid):
        super().__init__(f"No student found with ID {sid}.")

        # student_storage.py

import os
import pickle
from student_model import Student
from storage_interface import IStudentStorage # Import the abstract interface

class StudentStorage(IStudentStorage): # Implements the interface
    """
    Independent data persistence class, responsible for disk I/O.
    This is a Concrete class that implements the IStudentStorage interface.
    It uses pickle for serialization.
    """
    def __init__(self, data_file="students.data"):
        self.data_file = data_file

    def load_data(self) -> dict[str, Student]:
        """Loads student data from file (Implements interface method)"""
        students = {}
        if os.path.exists(self.data_file):
            try:
                with open(self.data_file, "rb") as f:
                    students = pickle.load(f)
                print(f"[{self.__class__.__name__}] Data loaded successfully from {self.data_file}!")
            except Exception as e:
                print(f"[{self.__class__.__name__}] Data loading failed: {e}")
        else:
            print(f"[{self.__class__.__name__}] No historical data, returning empty dictionary.")
        return students

    def save_data(self, students: dict[str, Student]):
        """Saves student data to file (Implements interface method)"""
        try:
            with open(self.data_file, "wb") as f:
                pickle.dump(students, f)
            print(f"[{self.__class__.__name__}] Data saved successfully to {self.data_file}!")
        except Exception as e:
            print(f"[{self.__class__.__name__}] Data saving failed: {e}")
