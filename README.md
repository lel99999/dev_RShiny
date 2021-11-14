# dev_RShiny
R Shiny WAF Staging and Development

#### Shiny on RHEL7
- base R install <br/>
`$sudo yum iinstall -y R`

- add shiny and rmarkdown packages <br/>
```
$sudo su - -c "R -e \"install.packages('shiny', repos='https://cran.rstudio.com/')\""
$sudo su - -c "R -e \"install.packages('rmarkdown', repos='https://cran.rstudio.com/')\""
```
![shiny-server page](https://github.com/lel99999/dev_RShiny/blob/main/shiny-server_01-300x376.png) <br/>

- update shiny-server.service to run as shiny <br/>
Edit file: /etc/systemd/system/shiny-server.service <br/>
```
ExecStart=/bin/bash -c 'setuid shiny; setgid shiny; /opt/shiny-server/bin/shiny-server --pidfile=/var/run/shiny-server.pid >> /var/log/shiny-server.log 2>&1'
```
- verify shiny-server is listening on default port 3838 <br/>
![shiny-server listening on 3838](https://github.com/lel99999/dev_RShiny/blob/main/shiny-server-port-01.png) <br/>

#### Shiny Application Components (put ui.R and server.R in folder <app_name>)
- A use-interface definition (UI) called ui.R
  - code used to set-up layout of web page
  - Title, sliders, widgets, plots, etc.  

- A server script file called sever.R
  - Code that does the computational R work "behind the scenes" using functions like hist(), plot()
  - code contains instructions to build the app

#### Deploy testApp to shiny-server
```
$scp -r testApp/ <shiny-server/ip-address>:/srv/shiny-server/
```
You should see the following: <br/>
![shiny-server testApp](https://github.com/lel99999/dev_RShiny/blob/main/shiny-server-testApp-01.png) <br/>

#### Docker Notes
- Building the image
```
docker build -t my-shinyapp-image .
```

- Starting the container
```
docker run -d --rm -p 3838:3838 my-shinyapp-image
```
