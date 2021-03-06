# solidity筆記

1.`===會出現 Expected primary expression錯誤，需要改為`\`==\`

2.public的array沒辦法被function return需寫為internal

\(但internal function 不會顯示，需要另外寫一個function去call他\)

```text
    Patient[] internal patients;
    function allPatients() internal constant returns(Patient[]) {
        return patients;
    }
```

3.有時會出現\`Error: new BigNumber\(\) not a base 16 number:\` 原因是return string時還沒sync

使用\`miner.start\(1\)\`讓他sync即可解決

ex:

```text
    function allPatients(uint16 i) constant returns(string) {
        return testss[i].name;
    }
```

4.無法直接進行字串比對，建議使用sha

[https://ethereum.stackexchange.com/questions/4559/operator-not-compatible-with-type-string-storage-ref-and-literal-string](https://ethereum.stackexchange.com/questions/4559/operator-not-compatible-with-type-string-storage-ref-and-literal-string)

ex:

```text
 if (sha3(patients[p].name) == sha3(name))
```

5.無法直接返回struct，須拆成多個參數返回

[https://ethereum.stackexchange.com/questions/3609/returning-a-struct-and-reading-via-web3/3614\#3614](https://ethereum.stackexchange.com/questions/3609/returning-a-struct-and-reading-via-web3/3614#3614)

ex:

```text
    function allPatients(uint16 i) constant returns(string, uint, uint, uint) {
        return (patients[i].name, patients[i].weight ,patients[i].height, patients[i].NHSNum);
    }
```

6.範例

```text
 pragma solidity ^0.4.16;


contract SalaryInfo {
    struct User {
        uint salaryId;
        string name;
        string userAddress;
        uint salary;
    }
    User[] public users;

    function addUser(uint _salaryId, string _name, string _userAddress, uint _salary) public returns(uint) {
        users.length++;
        users[users.length-1].salaryId = _salaryId;
        users[users.length-1].name = _name;
        users[users.length-1].userAddress = _userAddress;
        users[users.length-1].salary = _salary;
        return users.length;
    }

    function getUsersCount() public constant returns(uint) {
        return users.length;
    }

    function getUser(uint index) public constant returns(uint, string, string, uint) {
        return (users[index].salaryId, users[index].name, users[index].userAddress, users[index].salary);
    }
}
```

