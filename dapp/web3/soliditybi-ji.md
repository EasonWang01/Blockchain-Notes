1.`===會出現 Expected primary expression錯誤，需要改為`\`==\`

2.public的array沒辦法被function return需寫為internal

\(但internal function 不會顯示，需要另外寫一個function去call他\)

```
    Patient[] internal patients;
    function allPatients() internal constant returns(Patient[]) {
        return patients;
    }
```

3.有時會出現\`Error: new BigNumber\(\) not a base 16 number:\` 原因是return string時還沒sync

使用\`miner.start\(1\)\`讓他sync即可解決

ex:

```
    function allPatients(uint16 i) constant returns(string) {
        return testss[i].name;
    }
```

4.無法直接進行字串比對，建議使用sha

[https://ethereum.stackexchange.com/questions/4559/operator-not-compatible-with-type-string-storage-ref-and-literal-string](https://ethereum.stackexchange.com/questions/4559/operator-not-compatible-with-type-string-storage-ref-and-literal-string)

ex:

```
 if (sha3(patients[p].name) == sha3(name))
```

5.無法直接返回struct，須拆成多個參數返回

https://ethereum.stackexchange.com/questions/3609/returning-a-struct-and-reading-via-web3/3614\#3614

ex:

```
    function allPatients(uint16 i) constant returns(string, uint, uint, uint) {
        return (patients[i].name, patients[i].weight ,patients[i].height, patients[i].NHSNum);
    }
```



