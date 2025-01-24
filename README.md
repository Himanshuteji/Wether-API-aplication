# Wether-API-aplication
#Java Program
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URI;
import java.net.URISyntaxException;
import java.net.URL;
import java.util.Date;

//import java.util.Date;
import java.util.Scanner;

import com.google.gson.Gson;
import com.google.gson.JsonObject;


@WebServlet("/Servlet")

public class Servlet extends HttpServlet{
	
	private static final long serialVersionUID =1L;
	
	public Servlet() {
		super();
	}
	 
	protected void doGet(HttpServletRequest req,HttpServletResponse res) throws ServletException, IOException   
	{
		

	}

	protected void doPost(HttpServletRequest req,HttpServletResponse res) throws ServletException, IOException   
	{ 
	
		
		//API Setup
		String apiKey="95f50c99e72131f0062317da3dd2fa38";
		
		//html main se input liya 
		String city=req.getParameter("city");
//		System.out.println(city);
		  
		
		//Create URL for the the openWeatherMap API request
		 String apiUrl = "https://api.openweathermap.org/data/2.5/weather?q=" + city + "&appid=" + apiKey;
		 
	
	//APi Integration
try {
	 URI uri = new URI(apiUrl);
     URL url = uri.toURL();  // Convert URI to UR
       HttpURLConnection connection = (HttpURLConnection) url.openConnection();
       connection.setRequestMethod("GET");
       
       InputStream inputStream = connection.getInputStream();
       InputStreamReader reader = new InputStreamReader(inputStream);
       
             //want to store in string
       Scanner scanner = new Scanner(reader);
       StringBuilder responseContent = new StringBuilder();
             //input lene ke liye form tge reader , will create scanner
       
       while (scanner.hasNext()) {
           responseContent.append(scanner.nextLine());
       }
       
  
             scanner.close();
//             System.out.println(responseContent);  
             
             //Type casting jo weather string main aarha tha use ab hum JSON main chnge kare ge JavaScriptObjectNotation            
	
             Gson gson=new Gson();
             JsonObject jsonObject=gson.fromJson(responseContent.toString(), JsonObject.class);
             System.out.println(jsonObject);
             
             //Date & Time
             long dateTimestamp = jsonObject.get("dt").getAsLong() * 1000;
             Date date = new  Date(dateTimestamp);
             
             //Temperature 
             double temperatureKelvin=jsonObject.getAsJsonObject("main").get("temp").getAsDouble();
             int temperatureCelsius=(int) (temperatureKelvin-273);
             
             
             //Humidity
             int humidity = jsonObject.getAsJsonObject("main").get("humidity").getAsInt();
             
             //Wind Speed
             double windSpeed = jsonObject.getAsJsonObject("wind").get("speed").getAsDouble();
             
             //Weather Condition
             String weatherCondition=jsonObject.getAsJsonArray("weather").get(0).getAsJsonObject().get("main").getAsString();
             
             // Set the data as request attributes (for sending to the jsp page)
             req.setAttribute("date", date);
             req.setAttribute("city", city);
             req.setAttribute("temperature", temperatureCelsius);
             req.setAttribute("weatherCondition", weatherCondition); 
             req.setAttribute("humidity", humidity);    
             req.setAttribute("windSpeed", windSpeed);
             req.setAttribute("weatherData", responseContent.toString());
             
             connection.disconnect();
             
}
catch(IOException e) {
	e.printStackTrace();
} catch (URISyntaxException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}
// Forward the request to the weather.jsp page for rendering
req.getRequestDispatcher("index.jsp").forward(req, res);

	}

 
	

	
	}
	
#HTML content
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
    <link rel="stylesheet" href="styles.css">
</head>
<style>
/* Resetting default browser styles */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* Body styling */
body {
    font-family: 'Arial', sans-serif;
    background: linear-gradient(to right, #00c6ff, #0072ff);
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

/* Main container for the weather box */
.weather-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
}

/* Styling the weather box */
.weather-box {
    background: rgba(255, 255, 255, 0.8);
    padding: 30px 40px;
    border-radius: 15px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    text-align: center;
    width: 300px;
    animation: fadeIn 2s ease-out;
}

/* Heading style */
.weather-box h1 {
    font-size: 30px;
    color: #333;
    margin-bottom: 20px;
}

/* Styling the input field */
input[type="text"] {
    width: 80%;
    padding: 10px;
    border: 2px solid #0072ff;
    border-radius: 5px;
    margin-bottom: 15px;
    font-size: 16px;
    outline: none;
    transition: border-color 0.3s ease;
}

/* Focus effect on the input */
input[type="text"]:focus {
    border-color: #00c6ff;
}

/* Button styling */
.btn {
    width: 80%;
    padding: 12px;
    background-color: #0072ff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 18px;
    transition: background-color 0.3s ease, transform 0.3s ease;
}

/* Hover effect for button */
.btn:hover {
    background-color: #00c6ff;
    transform: translateY(-3px);
}

/* Hover effect for the input field */
input[type="text"]:hover {
    border-color: #00c6ff;
}

/* Styling for the weather info section */
.weather-info {
    margin-top: 20px;
    font-size: 18px;
    color: #333;
}

/* Animation for fading in */
@keyframes fadeIn {
    0% {
        opacity: 0;
    }
    100% {
        opacity: 1;
    }
}

</style>
<body>
    <div class="weather-container">
        <div class="weather-box">
            <h1>Weather App</h1>
            <form action="Servlet" method="post">
            <input type="text" id="city-input" placeholder="Enter city name" name="city">
            <button id="get-weather" class="btn">Get Weather</button>
            </form>
            <div id="weather-info" class="weather-info">
             
            </div>
        </div>
    </div>

   
</body>
</html>



#CSS
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
     <link rel="stylesheet" href="index.css" />
     <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
</head>



<body>

    <div class="mainContainer">
     <form action="Servlet" method="post" class="searchInput">
            <input type="text" placeholder="Enter City Name" id="searchInput" value="" name="city"/>
            <button id="searchButton"><i class="fa-solid fa-magnifying-glass"></i></button>
      </form>
      
        <div class="weatherDetails">
            <div class="weatherIcon">
            
                <img src="" alt="Clouds" id="weather-icon">
                <h2>${temperature} °C</h2>
                 <input type="hidden" id="wc" value="${weatherCondition}"> </input>
            </div>
            
            <div class="cityDetails">        
                <div class="desc"><strong>${city}</strong></div>
                <div class="date">${date}</div>
            </div>
            <div class="windDetails">
            	<div class="humidityBox">
            	<img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgr7XehXJkOPXbZr8xL42sZEFYlS-1fQcvUMsS2HrrV8pcj3GDFaYmYmeb3vXfMrjGXpViEDVfvLcqI7pJ03pKb_9ldQm-Cj9SlGW2Op8rxArgIhlD6oSLGQQKH9IqH1urPpQ4EAMCs3KOwbzLu57FDKv01PioBJBdR6pqlaxZTJr3HwxOUlFhC9EFyw/s320/thermometer.png" alt="Humidity">
                <div class="humidity">
                   <span>Humidity </span>
                   <h2>${humidity}% </h2>
                </div>
               </div> 
               
                <div class="windSpeed">
                    <img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiyaIguDPkbBMnUDQkGp3wLRj_kvd_GIQ4RHQar7a32mUGtwg3wHLIe0ejKqryX8dnJu-gqU6CBnDo47O7BlzCMCwRbB7u0Pj0CbtGwtyhd8Y8cgEMaSuZKrw5-62etXwo7UoY509umLmndsRmEqqO0FKocqTqjzHvJFC2AEEYjUax9tc1JMWxIWAQR4g/s320/wind.png">
                    <div class="wind">
                    <span>Wind Speed</span>
                    <h2> ${windSpeed} km/h</h2>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <script src="index.js"></script>
	  
</body>

</html>

#Index JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
     <link rel="stylesheet" href="index.css" />
     <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
</head>



<body>

    <div class="mainContainer">
     <form action="Servlet" method="post" class="searchInput">
            <input type="text" placeholder="Enter City Name" id="searchInput" value="" name="city"/>
            <button id="searchButton"><i class="fa-solid fa-magnifying-glass"></i></button>
      </form>
      
        <div class="weatherDetails">
            <div class="weatherIcon">
            
                <img src="" alt="Clouds" id="weather-icon">
                <h2>${temperature} °C</h2>
                 <input type="hidden" id="wc" value="${weatherCondition}"> </input>
            </div>
            
            <div class="cityDetails">        
                <div class="desc"><strong>${city}</strong></div>
                <div class="date">${date}</div>
            </div>
            <div class="windDetails">
            	<div class="humidityBox">
            	<img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhgr7XehXJkOPXbZr8xL42sZEFYlS-1fQcvUMsS2HrrV8pcj3GDFaYmYmeb3vXfMrjGXpViEDVfvLcqI7pJ03pKb_9ldQm-Cj9SlGW2Op8rxArgIhlD6oSLGQQKH9IqH1urPpQ4EAMCs3KOwbzLu57FDKv01PioBJBdR6pqlaxZTJr3HwxOUlFhC9EFyw/s320/thermometer.png" alt="Humidity">
                <div class="humidity">
                   <span>Humidity </span>
                   <h2>${humidity}% </h2>
                </div>
               </div> 
               
                <div class="windSpeed">
                    <img src="https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiyaIguDPkbBMnUDQkGp3wLRj_kvd_GIQ4RHQar7a32mUGtwg3wHLIe0ejKqryX8dnJu-gqU6CBnDo47O7BlzCMCwRbB7u0Pj0CbtGwtyhd8Y8cgEMaSuZKrw5-62etXwo7UoY509umLmndsRmEqqO0FKocqTqjzHvJFC2AEEYjUax9tc1JMWxIWAQR4g/s320/wind.png">
                    <div class="wind">
                    <span>Wind Speed</span>
                    <h2> ${windSpeed} km/h</h2>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <script src="index.js"></script>
	  
</body>

</html>

