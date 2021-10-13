# dev_RShiny
R Shiny WAF Staging and Development

#### Shiny Application Components (put ui.R and server.R in folder <app_name>)
- A use-interface definition (UI) called ui.R
  - code used to set-up layout of web page
  - Title, sliders, widgets, plots, etc.  
 


- A server script file called sever.R
  - Code that does the computational R work "behind the scenes" using functions like hist(), plot()
  - code contains instructions to build the app


#### Docker Notes
- Building the image
```
docker build -t my-shinyapp-image .
```

- Starting the container
```
docker run -d --rm -p 3838:3838 my-shinyapp-image
```
