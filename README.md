# EcoBrains Sensor

# Intro
This repository contains the source code for a waste analysis sensor system developed by @benjamin. 
The system integrates artificial intelligence and machine learning capabilities to automate waste classification processes for enterprises.

# Overview
The waste analysis sensor system is designed to analyze and classify various types of waste materials in real-time, 
providing accurate data to support waste management decisions. 
The system consists of a sensor device that collects waste data, processes it using machine learning algorithms, 
and transmits the results to a web server for analysis and storage.

# Features
Automated Waste Classification: The sensor system automates the process of waste classification using machine learning algorithms, reducing the need for manual intervention.
Real-time Data Analysis: Waste data is analyzed in real-time, providing up-to-date insights into waste composition and trends.
Secure Data Transmission: Data transmission between the sensor device and the web server is encrypted using AES encryption to ensure data security and privacy.
Flexible Reporting Schedule: The reporting schedule can be customized using environment variables, allowing for flexible reporting intervals based on operational needs.

# Usage
To use the waste analysis sensor system:

* Compile the Code: Compile the source code using a C compiler such as GCC.
  
  ```
  gcc -o ecoBrains-Sensor main.c
  ```

* Configure Environment Variables: Set environment variables for the reporting schedule and the URL of the web server portal.
* Deploy the Sensor System: Deploy the sensor system in the desired location for waste analysis.
* Monitor and Analyze Data: Monitor the sensor system's performance and analyze the collected data using the web server portal.
  
# Dependencies
The system relies on the following dependencies:

* [cJSON](https://github.com/DaveGamble/cJSON): A lightweight JSON parser for C.
* [curl](https://github.com/curl/curl): A library for transferring data with URLs.

# Source Code
You can download it [here](main.c)
```c
// Author: @benjamin 

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <unistd.h>
#include "cJSON.h"
#include <curl/curl.h>

// My Libraries
#include <sensorIdentificator> // Code to identify which sensor call APIs
#include <structWaste> // Waste object contain char type[20] and int quantity
#include <sensorData>  // Function to read data from sensor
#include <URLecobrainsPortal> // Url to website portal with data
#include <timePlanning> // Import a ENV Var to set time of report, based on cash plannig



// Function to send data to the web server via API
void send_report_to_webserver(Waste waste) {
    CURL *curl;
    CURLcode res;
    char *data;
    cJSON *json_data = cJSON_CreateObject();
    cJSON_AddStringToObject(json_data, "waste_type", waste.type);
    cJSON_AddNumberToObject(json_data, "quantity", waste.quantity);
    data = cJSON_PrintUnformatted(json_data);
    cJSON_Delete(json_data);

    // Initializing libcurl
    curl_global_init(CURL_GLOBAL_ALL);
    curl = curl_easy_init();
    if (curl) {
        curl_easy_setopt(curl, CURLOPT_URL, URLecobrainsPortal + "/api/report/submit_report");
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, data);
        curl_easy_setopt(curl, sensorIdentificator); // Identify which sensor
        res = curl_easy_perform(curl);
        if (res != CURLE_OK)
            fprintf(stderr, "Error sending report: %s\n", curl_easy_strerror(res));
        else
            printf("Report successfully sent to the web server.\n");
        curl_easy_cleanup(curl);
    }
    curl_global_cleanup();
    free(data);
}

int main() {
    // Send the report every x minutes, where x is the ENV Var
    while (1) {
        Waste waste;
        sensorData()(&waste);
        send_report_to_webserver(waste);
        sleep(timePlanning); // Wait for x minutes before sending the next report,where x is the ENV Var
    }

    return 0;
}
```
  
# Contributing
Contributions to the waste analysis sensor system are welcome! If you encounter any issues or have suggestions for improvements, please open an issue or submit a pull request.

# License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
