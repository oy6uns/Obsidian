page-server < object oriented cloud storage server
- logging bottleneck
- log/page server

### Colibri’s’ Solution
Seperate hot and cold data
- hot: page server
	- log per **record**
- cold: object storage(cloud)
	- log per **data block**


