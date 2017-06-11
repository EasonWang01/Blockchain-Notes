1.`===會出現 Expected primary expression錯誤，需要改為`\`==\`

2.public的array沒辦法被function return需寫為internal

```
    Patient[] internal patients;
    function allPatients() internal constant returns(Patient[]) {
        return patients;
    }
```



