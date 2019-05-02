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

def solar_irradiance(solar_constant, lattitude, day_number, hour):
    irradiance = solar_constant * Cos(zenith_angle(lattitude, day_number, hour))
    #if past sunset, irradiance is 0
    if irradiance < 0:
        return 0
    else:
        return irradiance

#VALIDATING THE MODEL
#the model predicts that LA, with lattitude of 34, on April 11(day 101)
#has a sunset (irradiance = 0) at 19.359 hour (19:21:32)
#which matches with provided data that puts the sunset at 19:21
print(solar_irradiance(1000, 34, 101, 19.359))

def day_curve(solar_constant, lattitude, day_number):
    data_list = []
    for i in range(1,240):
        i = i/10
        data_list.append(solar_irradiance(solar_constant, lattitude, day_number, i))
    return data_list
    
def graph_day_curve(solar_constant, lattitude, day_number):
    data_list = day_curve(solar_constant, lattitude, day_number)
    plt.plot(data_list)
    plt.xlim(0,240) 
    plt.xlabel("Hours")
    plt.ylabel("Irradiance")
    plt.title("Irradiance Over a Day at a Latitude of " + str(lattitude) + " on Day " + str(day_number))

def overlay_day_curves(solar_constant, lattitude):
    division = 12
    for i in range(division):
        graph_day_curve(solar_constant,lattitude,int(i*365/division))
    plt.xlabel("Hours")
    plt.ylabel("Irradiance")
    plt.title("Shifting of Daily Irradiance Curve Over a Year at a Lattitude of " + str(lattitude) + " Degrees")

def average_per_day(solar_constant, lattitude, day_number):
    data_list = day_curve(solar_constant, lattitude, day_number)
    sum = 0;
    for entry in data_list:
        sum += entry
    average = sum/len(data_list)
    return average

def year_curve(solar_constant, lattitude):
    data_list = []
    for day in range(1,365):
        data_list.append(average_per_day(solar_constant, lattitude, day))
    return data_list

def graph_year_curve(solar_constant, lattitude):
    data_list = year_curve(solar_constant, lattitude)
    plt.plot(data_list)
    plt.xlim(0,365) 
    plt.xlabel("Day")
    plt.ylabel("Average Irradiance")
    plt.title("Average Daily Irradiance Over a Year at a Latitude of " + str(lattitude))

def format():
    plt.title("Average Daily Irradiance Over a Year for a Few Cities")

#examples for each graphing function:
graph_day_curve(1000, 34, 150
overlay_day_curves(1000,34)

graph_year_curve(1000, 51.5) #London
graph_year_curve(1000, 40.7) #New York
graph_year_curve(1000, 34.0) #Los Angeles
graph_year_curve(1000, 25.7) #Miami
graph_year_curve(1000, 1.4)  #Singapore
graph_year_curve(1000,-33.8) #Syndney
graph_year_curve(1000,-41.3) #Wellington
format()
