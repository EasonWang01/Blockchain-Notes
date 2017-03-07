此為python寫的資料庫，需使用python3來安裝


#  #安裝


安裝相關套件

https://docs.bigchaindb.com/projects/py-driver/en/latest/quickstart.html

```
brew install python3

pip3 install bigchaindb_driver

安裝 libffi/ffi.h  (各平台不同，所以google安裝方法)

pip install --upgrade setuptools

```


使用docker啟動DB

https://docs.bigchaindb.com/projects/py-driver/en/latest/contributing.html
```
$ docker-compose up -d rdb
$ docker-compose up -d bdb-server
```

把port給出

https://docs.bigchaindb.com/projects/py-driver/en/latest/connect.html

執行完以下她會給你一個url記得把他填入以下範例程式
```
docker-compose port bdb-server 9984
```


之後試試看範例程式

```
from bigchaindb_driver import BigchainDB
from bigchaindb_driver.crypto import generate_keypair
bdb = BigchainDB('http://0.0.0.0:32773')


bicycle = {
  'data': {
    'bicycle': {
      'serial_number': 'abcd1234',
      'manufacturer': 'bkfab',
    },
  },
}

metadata = {'planet': 'earth'}

alice, bob = generate_keypair(), generate_keypair()


prepared_creation_tx = bdb.transactions.prepare(
       operation='CREATE',
       signers=alice.public_key,
       asset=bicycle,
       metadata=metadata,
   )




fulfilled_creation_tx = bdb.transactions.fulfill(prepared_creation_tx, private_keys=alice.private_key)

print(fulfilled_creation_tx)

sent_creation_tx = bdb.transactions.send(fulfilled_creation_tx)
```
