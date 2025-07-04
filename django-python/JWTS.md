# Djnago Rest Framework
## Install django rest framework
pip install djangorestframework

## Add rest_framework to your apps in settings.py
'rest_framework',

## Configure your main urls
    path('api/',include('api.urls')),
note api is the name of the app that contains the api code

# CRUD OPERATION 
create a model and add some objects to it

create a file named serializers.py in your app
import serializers and the model you want to work with.The serializers converts the model class into simple readable tables that the api can understand

from rest_framework import serializers
from to_do_list .models import Task

class Task_serializer(serializers.ModelSerializer):
    class Meta:
        model = Task
        fields = '__all__'

__in your views.py

from django.shortcuts import render
from django.http import JsonResponse
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .serializers import Task_serializer
from to_do_list .models import Task

@api_view(['GET'])
def api_overview(request):
    api_urls = {
        'List':'/task_list/',
        'Detail_view':'/task_detail/<str:pk>/',
        'Create':'/task_create/',
        'Update':'/task_update/<str:pk>/',
        'Delete':'/task_delete/<str:pk>/',
    }
    return Response(api_urls)

@api_view(['GET'])
def task_list(request):
    tasks = Task.objects.all()
    serializer = Task_serializer(tasks, many=True)
    return Response(serializer.data)

@api_view(['GET'])
def task_detail(request,pk):
    tasks = Task.objects.get(id=pk)
    serializer = Task_serializer(tasks, many=False)
    return Response(serializer.data)

@api_view(['POST'])
def task_create(request):
    serializer = Task_serializer(data=request.data)
    if serializer.is_valid():
        serializer.save()
    return Response(serializer.data)

@api_view(['PUT'])
def task_update(request,pk):
    task = Task.objects.get(id=pk)
    serializer = Task_serializer(instance=task, data=request.data)
    if serializer.is_valid():
        serializer.save()
    
    return Response(serializer.data)

@api_view(['DELETE'])
def task_delete(request,pk):
    tasks = Task.objects.get(id=pk)
    tasks.delete()

    return Response("items successfully deleted")

_______ ENDPOINTS
def End_points(request):
    data = [
        'blogs', 'blogs/:blogslug', 'blog_comments/:blogslug'
    ]
    return Response(data)
NOTE the data is based on what you want to do and how many endpoints you want your app to have


__ in your urls
urlpatterns = [
    path('', views.api_overview, name='api_overview'),
    path('task_list', views.task_list, name = "task_list"),
    path('task_detail/<str:pk>/', views.task_detail, name = "task_detail"),
    path('task_create', views.task_create, name = "task_create"),
    path('task_update/<str:pk>/', views.task_update, name = "task_update"),
    path('task_delete/<str:pk>/', views.task_delete, name = "task_delete"),
]

