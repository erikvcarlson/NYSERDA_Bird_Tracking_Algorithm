####################################################################################################################################################################
## Updating Packages and Installing -- NOTE need to run this first time to get R set up -- uncomment where needed. Might need to install additional packages below #
####################################################################################################################################################################

## Updating packages in R
#update.packages(ask = FALSE)

## Remove outdated Motus packages
#remove.packages(c("motus", "motusClient"))

## Installing New Motus packages

#install.packages("remotes")
library(remotes)

## install 'motus' package

#install_github("MotusWTS/motus")

## install 'motusData' package which contains sample datasets used in Chapter 7

#install_github("MotusWTS/motusData", force=T)

#################################################################################################
## Load Motus and associated packages (you might need to install some of these)
##################################################################################################

library(maps)
library(tidyverse)
library(rworldmap)
library(ggmap)
library(motus)
library(motusData)
library(lubridate)
library(dplyr)
library(DBI)
library(RSQLite)

Sys.setenv(TZ = "UTC")
setwd("/Users/erikcarlson/Research/Station_Data")  ## UPDATE TO YOUR WD


# Workflow here: https://motus.org/MotusRBook/accessingData.html

################################
## 3.5 Downloading tag detections
##################################

########################################################
## 3.5.1 Download data for a project for the first time
########################################################

proj.num <- 390 # Atlantic Offshore Wind Pilot

#proj.Recv < -"CTT-BB0BF3253D55"] #SE_Light

# Download data
#sql.motus <- tagme(projRecv = proj.num, new = TRUE, update = TRUE)
#sql.motus <- tagme(projRecv = "CTT-BB0BF3253D55", new = TRUE, update = TRUE) ##SE Light 
sql.motus <- tagme(projRecv = "CTT-59665269D53D", new = FALSE, update = TRUE) ## Black Rock 
#sql.motus <- tagme(projRecv = "CTT-AF0A181C34B3", new = TRUE, update = TRUE) ##Montauk

#login: pam.loring
# pw: Sterna3739!

## If you get an error message after entering user name and pw (above), 
## just run line 40 again and it should work 

##########################################################
## 3.5.3 Download data for a receiver for the first time
##########################################################

# See: https://motus.org/MotusRBook/accessingData.html

##########################################################
## 3.5.4 Downloading multiple receivers at the same time
###########################################################

# See https://motus.org/MotusRBook/accessingData.html

######################################################
## 3.5.5 Updating all .motus files within a directory
######################################################

## Once you have .motus files, 
## you can also update them all by simply 
## calling the tagme() function but leaving all arguments blank, 
## apart from the directory

## If you have them saved your working directory:
# tagme() ## THIS DOES NOT WORK SO JUST RUN COMMAND 56 WHEN YOU NEED TO UPDATE
## SETTING NEW=FALSE STILL UPLOADS EVERYTHING AGAIN SO JUST SET NEW=T

################################################
## 3.5.6 Accessing downloaded detection data
################################################

## specify the filepath where your .motus file is saved, and the file name.
file.name <- dbConnect(SQLite(), "./project_SEL.motus") 

## get a list of tables in the .motus file specified above.
dbListTables(file.name) 

# this retrieves the "alltags" table from the "sql.motus" 
#SQLite file we read in earlier
tbl.alltags <- tbl(sql.motus, "alltags") # virtual table

####################################################
## 3.5.8 Converting to flat file
####################################################

df.alltags <- tbl.alltags %>% 
  collect() %>% 
  as.data.frame()

# Format time stamp -- note UTC

df.alltags <- tbl.alltags %>% 
  collect() %>% 
  as.data.frame() %>%     # for all fields in the df (data frame)
  mutate(ts = as_datetime(ts, tz = "UTC", origin = "1970-01-01"))

str(df.alltags)

##########################################
# To select certain tag IDs:
###########################################

## filter to include only CTT Tag IDs from GBBG: 2A781E78, 1E333466, 66334B19
## Note - only includes data from tag deployment on gulls onward

# 4C551907 == 57951
# 4C34344B == 57952
# 61E62D7F == UKN 
df.alltagsSub <- df.alltags %>%
  filter(motusTagID %in% c("61E62D7F", "57265", "57951", "57952")) %>% 
  collect() %>% 
  as.data.frame()


## This makes a summary of detections and first/last dates per receiver and bird
df.alltagsSub %>% 
  group_by(mfgID, recvSiteName, tagDepComments) %>%
  summarize(firstDetUTC = min(ts), lastDetUTC = max(ts), numDets = length(ts)) %>%
  collect() %>%
  as.data.frame() 

## Save the detections file as a csv

write.csv(df.alltagsSub, "/Users/erikcarlson/Research/Station_Data/Black_Rock_Tag.csv", row.names=F)

## Save the detections file as an RDS (useful for further analysis in R)
##saveRDS(df.alltagsSub, "/Users/erikcarlson/Research/Station_Data/SEL_Tag.rds")
