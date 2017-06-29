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



