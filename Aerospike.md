# Skunkworks

## Aerospike

### Loading Data

```python
%%time
import sys
import csv
import aerospike

config = {
  'hosts': [ ('127.0.0.1', 3000) ]
}

client = aerospike.client(config).connect()


global rec
rec = {}
csvfile  = open('skunkworks.csv', "rb")
with open('skunkworks.csv', 'r') as f:
    reader = csv.reader(f) 
    rownum = 0
    for row in reader:
        # Save First Row with headers
        if rownum == 0:
            header = row
        else:
            colnum = 0
            for col in row:
                # print (rownum,header[colnum],col)
                rec[header[colnum]] = col
                colnum += 1
        rownum += 1
        # print(rownum,rec)
        if rec:
             client.put(('test', 'demo', str(rownum)), rec)
        rec = {}
csvfile.close()

```

### Reading Data

```python
%%time
def query_data(args):
    key, metadata, record = args
query = client.query('test', 'demo')
query.select('pickup','dropoff','distance','fare','p_long','p_lat','d_long','d_lat')
query.foreach(query_data)


```
