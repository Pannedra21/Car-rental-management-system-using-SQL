# Car-rental-management-system-using-SQL
App.views 
from django.shortcuts import render,redirect 
from django.contrib.auth import login ,logout,authenticate 
from django.contrib.auth.models import User 
from django.contrib.auth.decorators import login_required 
from django.contrib import messages 
from .models import car,Bookcar,allotcar,profile 
def home(request): 
return render(request,'home.html') 
def login_view(request): 
if request.method == "POST": 
username = request.POST.get('username') 
password = request.POST.get('password') 
user = authenticate(username=username,password=password) 
if user is not None: 
login(request,user) 
messages.success(request,'login successfull') 
return redirect('home') 
else: 
messages.error(request,'please check the details properly') 
return redirect('login') 
return render(request,'user.html') 
def admin_login_view(request): 
if request.method == "POST": 
username = request.POST.get('username') 
password = request.POST.get('password') 
user = authenticate(username=username,password=password) 
if user is not None: 
if user.is_staff: 
login(request,user)
return redirect('home') 
else: 
messages.error(request,"sorry you'r not admin/staff") 
return redirect('login') 
else: 
messages.error(request,'please check password | username') 
return redirect('Admin') 
return render(request,'admin.html') 
def logout_view(request): 
logout(request) 
return redirect('login') 
def Register_view(request): 
if request.method =='POST': 
First_Name = request.POST['name'] 
Email=request.POST['email'] 
username =request.POST['username'] 
password =request.POST['password'] 
confirmation_password =request.POST['confirm_password'] 
select_user=request.POST['select_user'] 
if select_user == 'admin': 
select_user=True 
else : 
select_user=False 
if password == confirmation_password: 
user = User.objects.filter(username=username) 
if user: 
messages.error(request,'username already exist use different') 
return redirect('register') 
else: 
user=User.objects.create_user( 
username=username, 
password=password,
email=Email, 
first_name=First_Name,is_staff=select_user) 
user.save() 
messages.success(request,'created account successfully') 
return redirect('login') 
else: 
messages.error(request,'password should same password twice') 
return redirect('register') 
return render(request,'register.html') 
def user_dash(request): 
return render(request,'rental.html') 
def add_cars(request): 
Addcar=True 
if request.method=="POST": 
Car=request.POST['car_name'] 
car_model=request.POST['car_model'] 
year=request.POST['car_year'] 
seater=request.POST['seater'] 
price=request.POST['car_price'] 
car_No=request.POST['car_number'] 
data=car.objects.create(car=Car,car_No=car_No,car_model=car_model,year=year,seater=seate 
r,price=price) 
data.save() 
return redirect('userdashboard') 
return render(request,'adminactions.html',{'Addcar':Addcar}) 
def remove_cars(request): 
Remove=car.objects.all() 
return render(request,'adminactions.html',{'Remove':Remove}) 
def remove_car(request,pk): 
Remove=car.objects.get(id=pk) 
Remove.delete() 
return redirect('removecars')
def create_profile(request): 
analys=True 
return render(request,'adminactions.html',{'analys':analys}) 
def analys_cars(request): 
analys=Bookcar.objects.all() 
return render(request,'history.html',{'History':analys}) 
def Book_car(request): 
if request.method=="POST": 
user=request.user 
Start_date=request.POST['period_start'] 
end_date=request.POST['period_end'] 
pickup_location=request.POST['pickup_location'] 
drop_location=request.POST['location'] 
seater=request.POST['seater'] 
car_info=request.POST['car_info'] 
pay_info=request.POST['payment_info'] 
data=Bookcar.objects.create(user=user,Start_date=Start_date,end_date=end_date,pickup_locat 
ion=pickup_location, 
drop_location=drop_location, 
seater=seater, 
car_info=car_info, 
pay_info=pay_info 
) 
data.save() 
return redirect('userdashboard') 
return render(request,'book.html') 
def Book_History(request): 
data=Bookcar.objects.filter(user=request.user) 
return render(request,'history.html',{'History':data,}) 
def cars_check(request): 
data=car.objects.all() 
return render(request,'browse.html',{'Available':data}) 
path('book-history',views.Book_History,name='bookhistory'), 
path('car-search',views.cars_check,name='cars_search'), 
path('profile',views.create_profile,name='profile') 
] 
App.models 
from django.db import models 
from django.contrib.auth.models import User 
class car(models.Model): 
car=models.TextField() 
car_model=models.TextField() 
car_No=models.TextField() 
year=models.TextField() 
seater=models.TextField() 
price=models.DecimalField(max_digits=6, decimal_places=2) 
def  str (self): 
return self.car 
class Bookcar(models.Model): 
user=models.ForeignKey(User, on_delete=models.CASCADE) 
Start_date=models.DateField(auto_now_add=False) 
end_date=models.DateField(auto_now_add=False) 
pickup_location=models.TextField() 
drop_location=models.TextField() 
seater=models.TextField() 
car_info=models.TextField() 
pay_info=models.TextField() 
status=models.TextField(default='pending') 
def   str  (self): 
return f"{self.user.username} from {self.Start_date} to {self.end_date}" 
class allotcar(models.Model): 
Book=models.ForeignKey(Bookcar, on_delete=models.CASCADE) 
car=models.ForeignKey(car, on_delete=models.CASCADE) 
class profile(models.Model): 
user=models.OneToOneField (User, on_delete=models.CASCADE) 
Full_Name=models.TextField() 
Number=models.TextField() 
Address=models.TextField() 
driving_L_No=models.TextField() 
Project.urls 
""" 
URL configuration for car_rental_online project. 
The `urlpatterns` list routes URLs to views. For more information please see: 
https://docs.djangoproject.com/en/5.0/topics/http/urls/ 
Examples: 
Function views 
1. Add an import: from my_app import views 
2. Add a URL to urlpatterns: path('', views.home, name='home') 
Class-based views 
1. Add an import: from other_app.views import Home 
2. Add a URL to urlpatterns: path('', Home.as_view(), name='home') 
Including another URLconf 
1. Import the include() function: from django.urls import include, path 
2. Add a URL to urlpatterns: path('blog/', include('blog.urls')) 
""" 
from django.contrib import admin 
from django.urls import path,include 
urlpatterns = [ 
path('admin/', admin.site.urls), 
path('',include('car_rental.urls')) 
] 
from django.conf import settings 
from django.conf.urls.static import static 
if settings.DEBUG: 
urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT) 
if settings.DEBUG: 
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 
Project.settings 
""" 
Django settings for car_rental_online project. 
Generated by 'django-admin startproject' using Django 5.0.6. 
For more information on this file, see 
https://docs.djangoproject.com/en/5.0/topics/settings/ 
For the full list of settings and their values, see 
https://docs.djangoproject.com/en/5.0/ref/settings/ 
""" 
from pathlib import Path 
# Build paths inside the project like this: BASE_DIR / 'subdir'. 
BASE_DIR = Path(  file  ).resolve().parent.parent 
# Quick-start development settings - unsuitable for production 
# See https://docs.djangoproject.com/en/5.0/howto/deployment/checklist/ 
# SECURITY WARNING: keep the secret key used in production secret! 
SECRET_KEY = 'django-insecure-yfyq6s-4*%f0atg@-o4rxhhtjawsfhf=gtj1mswi=e!fqty_07' 
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True 
ALLOWED_HOSTS = [] 
# Application definition 
INSTALLED_APPS = [ 
'django.contrib.admin', 
'django.contrib.auth', 
'django.contrib.contenttypes', 
'django.contrib.sessions', 
'django.contrib.messages', 
'django.contrib.staticfiles', 
'car_rental' 
] 
MIDDLEWARE = [ 
'django.middleware.security.SecurityMiddleware', 
'django.contrib.sessions.middleware.SessionMiddleware', 
'django.middleware.common.CommonMiddleware', 
'django.middleware.csrf.CsrfViewMiddleware', 
'django.contrib.auth.middleware.AuthenticationMiddleware', 
'django.contrib.messages.middleware.MessageMiddleware', 
'django.middleware.clickjacking.XFrameOptionsMiddleware', 
] 
ROOT_URLCONF = 'car_rental_online.urls' 
TEMPLATES = [ 
{ 
'BACKEND': 'django.template.backends.django.DjangoTemplates', 
'DIRS': [ BASE_DIR , 'template'], 
'APP_DIRS': True, 
'OPTIONS': { 
'context_processors': [ 
'django.template.context_processors.debug', 
'django.template.context_processors.request', 
'django.contrib.auth.context_processors.auth', 
'django.contrib.messages.context_processors.messages', 
], 
}, 
}, 
] 
WSGI_APPLICATION = 'car_rental_online.wsgi.application' 
# Database 
# https://docs.djangoproject.com/en/5.0/ref/settings/#databases 
DATABASES = { 
'default': { 
'ENGINE': 'django.db.backends.sqlite3', 
'NAME': BASE_DIR / 'db.sqlite3', 
} 
} 
# Password validation 
# https://docs.djangoproject.com/en/5.0/ref/settings/#auth-password-validators 
AUTH_PASSWORD_VALIDATORS = [ 
{ 
'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator', 
}, 
{ 
'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator', 
}, 
{ 
}, 
{ 
}, 
] 
'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator', 
'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator', 
# Internationalization 
# https://docs.djangoproject.com/en/5.0/topics/i18n/ 
LANGUAGE_CODE = 'en-us' 
TIME_ZONE = 'UTC' 
USE_I18N = True 
USE_TZ = True 
# Static files (CSS, JavaScript, Images) 
# https://docs.djangoproject.com/en/5.0/howto/static-files/ 
import os 
STATIC_URL = 'static/' 
STATICFILES_DIRS=[ 
os.path.join(BASE_DIR ,'static/') 
] 
MEDIA_URL ='/media/' 
MEDIA_ROOT=os.path.join(BASE_DIR ,'media/')
# Default primary key field type 
# https://docs.djangoproject.com/en/5.0/ref/settings/#default-auto-field 
DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'
