# Solar-Irradiance-Curve-Calculator
A python code that generates a plot of solar irradiance over time given location and date. 

import math
import matplotlib.pyplot as plt 

#define trigs in degrees
def Cos(a):
    return math.cos(math.radians(a))
def Sin(a):
    return math.sin(math.radians(a))
def ArcSin(a):
    return math.degrees(math.asin(a))
def ArcCos(a):
    return math.degrees(math.acos(a))

def hour_angle(hour, day_number): 
    #time is in 24 hour system. Ex. 4 pm is 16.
    #check for summer saving time
    #assume it starts on March 11 (an average of past few year's start data), which is day 70
    #assume it ends on November 4 (an average of past few year's start data), which is day 308
    if day_number >= 70 & day_number < 308:
        hour = hour - 1
    return 15*(hour-12)

def solar_declination(day_number):
    #day number as counted from Jan 1st. Ex. Feb. 1 has day number of 32
    return 23.45 * Sin(360 / 365 * (284 + day_number))

def zenith_angle(lattitude, day_number, hour):
    #the angle between the sun and the point directly overhead
    return ArcCos(Sin(lattitude) * Sin(solar_declination(day_number)) + Cos(lattitude) * Cos(solar_declination(day_number)) * Cos(hour_angle(hour, day_number)))

def solar_insolation(solar_constant, lattitude, day_number, hour):
    insolation = solar_constant * Cos(zenith_angle(lattitude, day_number, hour))
    #if past sunset, insolation is 0
    if insolation < 0:
        return 0
    else:
        return insolation

def print_day_data(solar_constant, lattitude, day_number):
    data_list = []
    for i in range(1,240):
        i = i/10
        data_list.append(solar_insolation(solar_constant, lattitude, day_number, i)) 
    plt.plot(data_list)
    plt.xlim(0,240) 
    plt.xlabel("Hours (multiplied by 10)")
    plt.ylabel("Solar Insolation")
    plt.title("Solar Insolation Over a Day With Latitude of " + str(lattitude) + " on Day " + str(day_number))

#VALIDATING THE MODEL
#the model predicts that LA, with lattitude of 34, on April 11(day 101)
#has a sunset (insolation = 0) at 19.359 hour (19:21:32)
#which matches with timeanddate.com's model that puts the sunset at 19:21
#print(solar_insolation(1000, 34, 101, 19.359))

print_day_data(1000, 34, 101)

