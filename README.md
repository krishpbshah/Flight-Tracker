# Flight Tracker

Flight Tracker is a Django-based web application designed to provide airlines with real-time insights into flight schedules and passenger information. It is built to enhance airline operations by facilitating real-time decision-making and improving the overall passenger experience through its user-friendly design.

## Features

- **Real-time Flight Tracking:** The system offers real-time tracking of flight schedules, allowing airlines to stay updated on the status of their flights.

- **Passenger Information:** Flight Tracker provides a comprehensive solution for storing and managing passenger information, streamlining the passenger handling process.

- **Optimized System:** Engineered with Django's Object-Relational Mapping (ORM) and SQL databases, the system is optimized for scalability and responsiveness. This ensures efficient data management and quick access to critical information.

- **User-Friendly Design:** The user interface is designed with a focus on intuitiveness and visual appeal. CSS is incorporated to enhance the overall user experience.

## Technologies Used

- Django: A high-level Python web framework for rapid development.
- SQL: Structured Query Language for efficient database operations.
- CSS: Cascading Style Sheets for styling the user interface.

#UI

<img width="1468" alt="Screenshot 2024-01-14 at 3 23 12 PM" src="https://github.com/krishpbshah/Flight-Tracker/assets/64669781/85f8f5c0-1a19-4c93-9c60-bb6171957e09">
<img width="1470" alt="Screenshot 2024-01-14 at 3 23 25 PM" src="https://github.com/krishpbshah/Flight-Tracker/assets/64669781/2a7813a5-bb38-4efd-a57c-c4b1a4a5e346">
<img width="1470" alt="Screenshot 2024-01-14 at 3 24 33 PM" src="https://github.com/krishpbshah/Flight-Tracker/assets/64669781/55603025-f164-4eae-92d4-64f95b9b50b2">
<img width="1144" alt="Screenshot 2024-01-14 at 3 24 43 PM" src="https://github.com/krishpbshah/Flight-Tracker/assets/64669781/3625f134-f18c-4c0c-acc9-d3ef7670c8bf">
<img width="1141" alt="Screenshot 2024-01-14 at 3 24 52 PM" src="https://github.com/krishpbshah/Flight-Tracker/assets/64669781/c0970a45-ad42-4094-b4f3-ec47574e70d6">
<img width="1141" alt="Screenshot 2024-01-14 at 3 25 28 PM" src="https://github.com/krishpbshah/Flight-Tracker/assets/64669781/86d37fff-0534-48e9-a6e5-86f9b3342d1a">



## Main Code:

- Models In Django For Functionality:
  from django.db import models



class Airport(models.Model):
    code = models.CharField(max_length=3)
    city = models.CharField(max_length=64)

    def __str__(self):
        return f"{self.city} ({self.code})"

class Flight(models.Model):
    origin = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name="departures")
    destination = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name="arrivals")
    duration = models.IntegerField()

    def __str__(self):
        return f"{self.id}: {self.origin} to {self.destination}"

class Passenger(models.Model):
    first = models.CharField(max_length=64)
    last = models.CharField(max_length=64)
    flights=models.ManyToManyField(Flight, blank=True, related_name="passengers")

    def __str__(self):
        return (f"{self.first} {self.last}")

View.py File For Functions and HTML Docs:
from django import forms
from django.http import HttpResponseBadRequest, HttpResponseRedirect, Http404
from django.shortcuts import render
from django.urls import reverse

from .models import Flight, Passenger

# Create your views here.
def index(request):
    return render(request, "flights/index.html", {
        "flights": Flight.objects.all()
    })


def flight(request, flight_id):
    try:
        flight = Flight.objects.get(id=flight_id)
    except Flight.DoesNotExist:
        raise Http404("Flight not found.")
    return render(request, "flights/flight.html", {
        "flight": flight,
        "passengers": flight.passengers.all(),
        "non_passengers": Passenger.objects.exclude(flights=flight).all()
    })

from django.shortcuts import render, get_object_or_404

def book(request, flight_id):
    if request.method == "POST":
        passenger_id = request.POST.get("passenger")
        if passenger_id:
            passenger = get_object_or_404(Passenger, pk=int(passenger_id))
            flight = get_object_or_404(Flight, pk=flight_id)
            passenger.flights.add(flight)
            return HttpResponseRedirect(reverse("flight", args=(flight_id,)))
        else:
            return HttpResponseBadRequest("Bad Request: no passenger chosen")

    # Handle GET request if needed
    # ...



