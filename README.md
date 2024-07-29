# React-Django-Connection
React + Django Connection

To connect a Django backend with a React frontend and display data from a Django model in a React application, you need to set up a few components:
1.	Django Backend:
o	Define a Django model.
o	Create a Django view to handle API requests.
o	Set up a Django URL route for the API endpoint.
o	Use Django Rest Framework (DRF) to serialize data and create API views.
2.	React Frontend:
o	Fetch data from the Django API using fetch or a library like Axios.
o	Display the fetched data in your React components.
Here’s a step-by-step guide to connect your Django and React projects:
1. Set Up Django Backend
a. Install Django Rest Framework
Make sure you have Django Rest Framework installed:

pip install djangorestframework

Add 'rest_framework' to your INSTALLED_APPS in settings.py:
INSTALLED_APPS = [
    ...
    'rest_framework',
    ...
]

b. Define the Django Model
In your models.py, define the Employee model:
from django.db import models

class Employee(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    email = models.EmailField()
    position = models.CharField(max_length=100)

    def __str__(self):
        return f"{self.first_name} {self.last_name}"

c. Create a Serializer
Create a serializers.py file in your Django app directory and define a serializer for your model:
from rest_framework import serializers
from .models import Employee

class EmployeeSerializer(serializers.ModelSerializer):
    class Meta:
        model = Employee
        fields = '__all__'

d. Create a View
In views.py, create a view to handle API requests:
from rest_framework import generics
from .models import Employee
from .serializers import EmployeeSerializer

class EmployeeListView(generics.ListAPIView):
    queryset = Employee.objects.all()[:5]  # Limit to 5 employees
    serializer_class = EmployeeSerializer

e. Define URL Routes
In urls.py, create a URL route for your API endpoint:
from django.urls import path
from .views import EmployeeListView

urlpatterns = [
    path('api/employees/', EmployeeListView.as_view(), name='employee-list'),
]

2. Set Up React Frontend
a. Create a New React App
If you haven't already created a React app, you can create one using Create React App:

npx create-react-app my-react-app
cd my-react-app
b. Fetch Data from Django API
In your React component, use fetch or Axios to request data from the Django API. For example, using fetch:

import React, { useEffect, useState } from 'react';

function EmployeeList() {
    const [employees, setEmployees] = useState([]);

    useEffect(() => {
        fetch('http://localhost:8000/api/employees/')
            .then(response => response.json())
            .then(data => setEmployees(data))
            .catch(error => console.error('Error fetching data:', error));
    }, []);

    return (
        <div>
            <h1>Employee List</h1>
            <ul>
                {employees.map(employee => (
                    <li key={employee.id}>
                        {employee.first_name} {employee.last_name} - {employee.email} - {employee.position}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default EmployeeList;

c. Run Both Servers
Make sure your Django server is running:

python manage.py runserver
npm start


3. Configure CORS
To allow your React app to communicate with your Django backend, you need to configure CORS (Cross-Origin Resource Sharing). Install and configure django-cors-headers:

pip install django-cors-headers
Add 'corsheaders' to INSTALLED_APPS and add the middleware in settings.py:
INSTALLED_APPS = [
    ...
    'corsheaders',
    ...
]

MIDDLEWARE = [
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
]

CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",  # React development server URL
]


