# Machine-Data-Storage-and-Display
**※The code property belongs to the lab, so it is not allowed to open source.**

This project cooperates with three other groups
* I am responsible for publishing the voltage data to the broker through MQTT and using matplotlib to display the data locally.
* The first group will intercept packets and forge them when there is no encryption. Otherwise, they will prove that packets are not able to access.
* The second group utilizes python to set up a broker on a Raspberry Pi to receive data and insert it into Mysql.
* The third group will get the data and display it remotely by Power BI.
![image](https://user-images.githubusercontent.com/88305396/152937965-6471c28a-95a7-4bce-9e5d-22051040e11f.png)

# Data transmission
This machine is the same as [Automatic-Picking-System](https://github.com/Elmo6ooo/Automatic-Picking-System), the punch of this machine is controlled by the voltage level. When the voltage value is higher, the punch will be higher, and vice versa.

To confirm whether the upper and lower limits of stamping are reached, there is a function responsible for continuously capturing the voltage value, so I declare two arrays that this function can store current-time and current-voltage. After a stamp, data will be written in a file also published to the broker then the arrays can be cleared.

In the part of MQTT transmission, I am using [M2Mqtt](https://www.nuget.org/packages/M2Mqtt/) from NuGet. Since it can only publish byte array, I encode 1000 strings consisting of ',' and '-' as split symbols into a byte array, and then publish to Broker.

In the Broker part, I first decode the received content, then divide it into multiple pieces of data through `split('-')` and pack each piece of data into a tuple list, and then Insert it in Mysql through `executemany(sql, data)` at once.

# Local data display
In this part, I declare a function that directly read the file written previously and draw the line graph by the specific parameters we gave.

First, data will be divided into rows, and then if it conforms to the parameters we gave, the voltage and time will be stored into the corresponding array. To draw multiple stamps in a single graph, I let the time array of all stamping start at 0 by each element subtracting the first element. Finally, plot two arrays using matplotlib.

In addition, unwanted data can be filtered, as the figure below only shows voltage less than two.
![圖片1](https://user-images.githubusercontent.com/88305396/153044901-eaa9560a-3163-47fa-bfbd-ec89ab0da6ae.png)

# Machine data storage and display demo
The video makes sure the table is blank. After selecting three objects to stamp, a Raspberry Pi remote window is open, and it shows that data are Insert into Mysql after each stamp, then the chart is stored on the desktop, and then we go back and check the database.


https://user-images.githubusercontent.com/99638331/154641527-52d8a912-eaac-4cba-954a-b9e504b5d853.mp4

