# Introduction

The ALA Spatial Portal (SP) is used as an interactive tool for mapping and analysis of the occurrence, contextual, environmental and other (e.g. user) data. As such, we need a regular report of the usage of the SP, it's parts and data for better structuring and focusing on the used sections of the portal.


# Details

For the usage reports, there needs to be 2 parts:
  1. analyse samples download file (zip) and send information to the global ALA Logger service
  1. generate a detailed report of the usages of the SP for the team to better understand it's usages and focus development

To achieve the above tasks, there is currently a logging process within the SP  that logs various user activities, including:
  * Date (ISO-8601)
  * User IP
  * User Email
  * Process ID
  * Session ID
  * Action type
  * Species LSID mapped
  * Layers mapped
  * Analysis method
  * Analysis params
  * Samples download file
  * Messages

For **#1**, a cron job that is executed at the End Of Day to analyse the sample files for each day and send the data to the ALA Logger service

For **#2**, the following view may help with the review of usage:

  * Group by Analysis Method
  * Group by Species
  * Group by Layers
  * Group by User IP
  * Group by Hour (time)

Another possible view for the report would be to generate a "user session" based on the activities. This can be achieved using the user\_ip and the time factors.