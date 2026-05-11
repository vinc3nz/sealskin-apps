FROM lscr.io/linuxserver/winegui:latest

# Set environment variables
ENV WINEPREFIX=/config/wine
ENV WINEARCH=win64

# Install prerequisites
USER root
RUN apt-get update && apt-get install -y \
    wget \
    unzip \
    cabextract \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -p /app/office && chown -R abc:abc /app/office \
    && chown -R abc:abc /config

# Switch back to abc user
USER abc

# Create directory for Office installation
# RUN mkdir -p /app/office

# Download Office Deployment Tool
RUN wget -O /app/office/odt.exe https://download.microsoft.com/download/2/7/A/27AF1BE6-DD20-4CB4-B154-EBAB8A7D4A7E/officedeploymenttool_17328-20162.exe

# Extract ODT
RUN cabextract /app/office/odt.exe -d /app/office/

# Create Office installation configuration for OneNote only
RUN echo '<?xml version="1.0" encoding="UTF-8"?>\n\
<Configuration>\n\
  <Add OfficeClientEdition="64" Channel="Current">\n\
    <Product ID="O365ProPlusRetail">\n\
      <Language ID="en-us" />\n\
      <ExcludeApp ID="Word" />\n\
      <ExcludeApp ID="Excel" />\n\
      <ExcludeApp ID="PowerPoint" />\n\
      <ExcludeApp ID="Access" />\n\
      <ExcludeApp ID="Outlook" />\n\
      <ExcludeApp ID="Publisher" />\n\
      <ExcludeApp ID="Teams" />\n\
    </Product>\n\
  </Add>\n\
  <Property Name="SharedComputerLicensing" Value="0" />\n\
  <Property Name="SCLCacheOverride" Value="0" />\n\
  <Property Name="DeviceBasedLicensing" Value="0" />\n\
  <Updates Enabled="TRUE" />\n\
  <RemoveMSI />\n\
  <Display Level="None" AcceptEULA="TRUE" />\n\
</Configuration>' > /app/office/config.xml

# Install Office (OneNote only)
RUN wine /app/office/setup.exe /configure /app/office/config.xml

# Clean up installation files
RUN rm -rf /app/office/odt.exe /app/office/setup.exe /app/office/config.xml

# Copy Office activation script
COPY activate-office.sh /app/office/activate-office.sh
RUN chmod +x /app/office/activate-office.sh

# Expose port
EXPOSE 3000

# Set the default command
CMD ["/init"]