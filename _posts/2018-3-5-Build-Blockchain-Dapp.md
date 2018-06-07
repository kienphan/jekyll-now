---
layout: post
title: Build 1 ứng dụng Blockchain hoàn chỉnh trên nền Ethereum Network
---

Đang trong lúc ngâm cứu về Solidity, Ethereum, cũng nhân tiện mấy vụ đất đai đang hot trên mạng, mình nảy sinh ý tưởng bá chủ toàn cầu, nghĩ ra game đơn giản gọi là Crypto World, thích thằng nào là mình mua luôn. Trong tutorial này mình sẽ trình bày 1 cách đơn giản triển khai từ ý tưởng đến lúc deploy hoàn thành.

Cái game hoàn thành sẽ trông như thế này (Do làm trong mấy ngày để ra Tut nên còn đơn giản)

![_config.yml]({{ site.baseurl }}/images/cryptoworld.gif)

Mình hi vọng Tutorial này sẽ giúp ta có cái nhìn tổng quan để tạo 1 DApp hoàn chỉnh trên nền Ethereum Network, do là Tutorial nên đối tượng là người đã có chút hiểu biết về Ethereum và Blockchain ở các mức khái niệm, vì mình sẽ không giải thích lại nữa, đầy rẫy trên internet rồi.

### Các công nghệ sẽ được sử dụng trong tutorial này:
- Database: Ethereum Rosten tesnet blockchain.
- Hosting: IPFS, nền tảng Hosting phi tập trung
- Frontend: HTML&CSS&javascript basic như thường, do đang đơn giản nên cũng không sử dụng modern tools các thứ như webpack làm gì, config phức tạp hơn.
- Smart Contract: Solidity ^0.4.20
- Frontend contracts API: web3.js để tương tác với smartcontract thông qua giao diện web
- Smartcontract Frameworks: Truffle, framework phổ biến nhất hiện tại, ngoài ra thì có thể dùng 1 framework khác như là Embark
- Development server: Node.js, ở đây dùng lite-server để serve static web
- Metamask: plugin Chrome, tương tác với Ví của bạn để làm thao tác thanh toán bằng Ethereum
- 3rd library: có sử dụng library của [Jsmaps](https://jsmaps.io) để tạo bản đồ TG


### Các bước để hoàn thành series này:
- Setup
- Code smart contract với Solidity
- Tạo ứng dụng frontend(HTML&CSS)
- Deploy ứng dụng lên IPFS
- Dùng thử DApp xem nó thế nào và tổng kết

## 1. Setup

Ý tưởng Game Crypto World: 

> Bạn muốn thành địa chủ của cái Quả Đất này, đơn giản thôi, hãy dùng Ethereums mua tất những nơi mày muốn, chỉ cần trả giả cao hơn thằng khác là được. Trong tương lai, có thể thêm các tính năng bảo vệ vùng đất khỏi thằng khác mua lại bằng các Câu Đố Bảo Vệ và xếp hạng các địa chủ của Thế giới.

Ý tưởng ban đầu khá phức tạp, vậy nên mình sẽ phải thực hiện từ mức đơn giản là mua chiếm vùng đất trược đã(phạm vi Tutorial này)

Đảm bảo rằng môi trường trước hết cần có `Node.js`

Install trufle framework với `npm`

```
npm i -g truffle
```

Chi tiết hơn, có thể đọc [tại đây](http://truffleframework.com/docs/getting_started/installation)

Tạo thư mục chính của Project 

```
cd /your-dev/path
mkdir cryptoworld
cd /your-dev/path/cryptoworld
```

Có 2 cách để tạo ra Smart contract Project, from scratch hoặc template

> Scratch

```
truffle init
```

> Template 

Từ template gọi là các `truffle box`, ở đây mình dùng cách này cho đơn giản, vì mình thấy series `pet-shop` này cũng tương tự

```
truffle unbox pet-shop
```

### Cấu trúc thư mục

1 project Truffle sẽ có các thư mục như sau:

- `contracts/`: chứa các source file Smart contract viết bằng Solidity, có sẵn 1 file gọi là Migrations.sol
- `migrations/`: Truffle sử dụng migration để xử lý deploy smart contract deployments.
- `test/`: Chứa cả JavaScript(mocha.js) và Solidity unit tests cho smartcontract
- `truffle.js`: Truffle Configuration file

Sau khi clone template xong thì có những task cơ bản như sau:
- Chạy thử template với lite-server
- Tạo bản đồ chạy được từ thư viện `Jsmaps`
- Set nickname người chơi (Phần sau)
- Tiến hành mua các nước bằng Ethereum (Phần sau)

### Chạy thử template với lite-server

Install lại các dependencies cần thiết
```
npm install
```

Khởi động lite-server 
```
npm run dev
```

Truy cập `localhost:3000` để xem có gì xẩy ra

### Tạo bản đồ chạy được từ thư viện `Jsmaps`

Truy cập vào `localhost:3000`, bạn sẽ thấy đấy hoàn toàn là template, vậy cần phải sửa lại nó để hiển thị bản đồ như ý muốn của ta bằng cách download library từ `jsmaps` rồi nhập vào.

Do phần này liên quan đến kỹ năng web cơ bản nên mình ko muốn trình bày dài, có thể tham khảo ở repo của mình

https://github.com/kienphan/cryptoworld


## 2. Code Smart Contract với Solidity

Trước tiên xoá những smart contract thừa thãi từ template đi hết.

Để đơn giản trước tiên mình thiết kế 1 smart contract duy nhất, gọi là `KuniLord.sol`, mình định nghĩa 1 vùng đất là 1 `Kuni`. 
Nếu xem smart contract tương ứng như khái niệm Class trông OOP, thì việc thiết kế smart contract cũng gần tượng tự như thiết kế Class vậy.

```Javascript
pragma solidity ^0.4.20;

contract KuniLord {
    // TODO
}
```

### Thiết lập các thuộc tính là thông tin của Smart Contract

```C++
pragma solidity ^0.4.20;

contract KuniLord {
    uint private constant INIT_BUY_PRICE = 1000;
    address public owner;
    struct Lord {
        address addr;
        bytes32 nickname;
        uint totalSpending;
    }
    
    struct Kuni {
        bytes32 name;
        Lord lord;
        uint currentBuyPrice;
    }
    mapping (address => Lord) lords;
    mapping (bytes32 => Kuni) kunies;
    
    Kuni[] public occupiedKunies;
    Lord[] public occupingLords;
}
```

Giải thích khái quát về các thông tin
- Giá khởi điểm của mỗi vùng đất `INIT_BUY_PRICE`, 1000 GWei (1 ether = 10^9 gwei)
- `owner`: smart contract owner address, tức địa chỉ của người deployed contract đấy
- Vì kết tập 2 thông tin là `Lord` và `Kuni` nên add thêm 2 `struct Lord` và `struct Kuni` tương ứng vào, các thông tin cũng dễ hiểu
- `mapping`, mapping là thứ mà khi ban đầu tiếp cận với Solidity có thể gây khó hiểu cho nhiều người nhất, nhưng hiểu đơn giản như tên, thì nó như là 1 Hash theo kiểu `key-value`
- Lưu trữ các thông tin struct Kuni và Lord đã đăng kí vào các mảng tương ứng với `occupiedKunies` và `occupingLords`

### Constructor khi khởi tạo contract

```C++
constructor() public {
    owner = msg.sender;
}
```

### Set Nickname và get Nickname người chơi

```C++
function setNickname(bytes32 _nickname) public payable {
    lords[msg.sender].addr = msg.sender;
    lords[msg.sender].nickname = _nickname;
    emit SetNickname(msg.sender, _nickname);
}

function getNickname() public view returns (bytes32) {
    return lords[msg.sender].nickname;
}
```

### Kiểm tra 1 Kuni đã bị chiếm đóng và lưu trên blockchain hay chưa

```C++
function checkKuniExist(bytes32 _name) internal view returns(bool){
    for (uint i = 0; i< occupiedKunies.length; i++) {
        if (occupiedKunies[i].name == _name) {
            return true;
        }
    }
    return false;
}
```

### Lấy thông tin của 1 Kuni

Bao gồm việc Kuni đang bị ai chiếm đóng, thằng đấy địa chỉ ở đâu, giá hiện tại là bao nhiêu

```C++
function getKuniInfo(bytes32 kuni) 
public 
view 
returns(bytes32 name, address lordAddr, bytes32 lordName, uint currentBuyPrice) {
    require(kuni != "");
    if (checkKuniExist(kuni)) {
        return (kuni, 
              kunies[kuni].lord.addr, 
              kunies[kuni].lord.nickname, 
              kunies[kuni].currentBuyPrice);
    }
    return (kuni, 0, 0, kunies[kuni].currentBuyPrice);
}
```

### Kiếm tra 1 Lord đã tồn tại chưa

```C++
function checkLordExist(address lordAddr) internal view returns(bool) {
    for(uint i = 0; i < occupingLords.length; i++) {
        if (occupingLords[i].addr == lordAddr) {
            return true;
        }
    }
    return false;
}
```

### Cuối cùng hàm quan trọng nhất là hàm mua Kuni

```C++
function occupy(bytes32 nation) public payable {
    require(nation != "");
    if (checkKuniExist(nation)) {
        require(msg.value > kunies[nation].currentBuyPrice);
            
        kunies[nation].currentBuyPrice = msg.value;
        kunies[nation].lord = lords[msg.sender];
    } else {
        require(msg.value > INIT_BUY_PRICE);
            
        Kuni memory newKuni = Kuni({
            name: nation,
            currentBuyPrice: msg.value,
            lord: lords[msg.sender]
        });
            
        kunies[nation] = newKuni;
        occupiedKunies.push(newKuni);
    }
    lords[msg.sender].totalSpending += msg.value;
    msg.sender.transfer(msg.value);
    if (!checkLordExist(lords[msg.sender].addr)) {
        occupingLords.push(lords[msg.sender]);
    }
    emit OccupyKuni(msg.sender, nation, msg.value);
}
```

Smart Contract hoàn chỉnh có thể tham khảo ở đây

https://github.com/kienphan/cryptoworld/blob/master/contracts/KuniLord.sol


Việc viết smart contract đã xong, giờ có 2 cách để test:

- Viết tests dùng truffle và deploy contract với `testrpc` 
- Test bằng tay với Remix IDE https://remix.ethereum.org. Đây là hàng chính chủ của Ethereum để viết Smart contract với Solidity, test, deploy.

Để đơn giản mình sẽ dùng Remix IDE để test nhanh các funtion và deploy luôn. Thực tế thì smart contract phải luôn được test kỹ lưỡng mới có thể triển khai được, do đặc thù của blockchain là không thể sửa đổi.

Để có thể tiếp tục, chúng ta phải cài `metamask.io`, là tool để tương tác với ví Ethereum của bạn với smart contract. Phần cài đặt và khởi tạo `Metamask`, mình sẽ bỏ qua. Do Metamask có tạo cả account trên mainnet nên hãy nhớ bảo mật tài khoản của mình nếu có sử dụng sau này.

Giao diện Remix IDE sẽ trông như thế này

![_config.yml]({{ site.baseurl }}/images/remix.png)

Chắc chắn rằng trên metamask đã chuyển sang dùng Ropsten Testnet

![_config.yml]({{ site.baseurl }}/images/select-ropsten.png)

Bạn có thể đến trang https://faucet.metamask.io/ để request free 1 ETH trên testnet, như mình cũng có khoảng 8 ETH, tầm 4k8 $ Trump, làm cá con được rồi. :D 

![_config.yml]({{ site.baseurl }}/images/ropsten.png)

Trên Remix IDE chắc chắn là đã chọn môi trường là `Injected web3` để nhúng blockchain từ Metamask vào

![_config.yml]({{ site.baseurl }}/images/web3-ropsten.png)

Trên Remix, tab Run, click `Deploy`, để Deploy smart contract lên blockchain, ta sẽ thấy popup metamask bật lên. Thời gian deploy có thể nhanh hoặc chậm tuỳ vào tình hình network, phí GAS bạn trả là nhiều hay ít.

![_config.yml]({{ site.baseurl }}/images/deploy-contract.png)

Contract instance khi deploy xong sẽ có thể thấy như thế này

![_config.yml]({{ site.baseurl }}/images/contract-instance.png)

Ta có thể điền tham số, `bytes32`, `string` vào để thử test các returns của các `function` đã chạy đúng chưa

## 3. Frontend App với HTML&CSS

Do phần đầu mình đã nêu rút gọn phần tạo bản đồ, cho nên phần này hợp với phần đầu tiên luôn

### Kết nối phần ứng dụng với blockchain

Ta có thể sử dụng truffle để deploy contract và lấy thông tin, nhưng như thế thì đồng nghĩa ta phải tạo ra 1 node, và phải đồng bộ mạng Blockchain về node (phải hơn chục GB và 2-3h sync), nên ở đây ta sẽ lựa chọn deploy contract bằng Remix IDE

### Copy ABI từ contract

Từ Tab Compile trên Remix IDE, click `Detail` để thấy chi tiết về contract, ta copy ABI

![_config.yml]({{ site.baseurl }}/images/abi.png)

Paste ra 1 file `JSON`, gọi là KuniLord.json, mình để nó ở cùng cấp với `index.html`

Trong `/js/app.js`, định nghĩa cái ABI này cùng với contract address mà ta đã deploy

```javascript
$.getJSON('KuniLord.json', function(data) {
  // Get the necessary contract artifact file and instantiate it with truffle-contract
  var kuniLordArtifact = data;
  var kuniLordContract = web3.eth.contract(kuniLordArtifact);

  // Cái address phía dưới là địa chỉ mà ta đã deploy
  App.contracts.kuniLordInstance = kuniLordContract.at("0x03d769b962efbf5f4844c161c809c41edaf3ab57")

      ...
}
```

Vậy là dù trên `localhost:3000`, thì app của bạn cũng đã trực tiếp thao tác trên blockchain rồi đấy.

## 4. Deploy ứng dụng lên IPFS

Ứng dụng đã hoàn thành, bây giờ phải làm cho nó online với tất cả mọi người, mà lại chẳng muốn tốn thêm chi phí :'( Với static web thì ta có thể nghĩ ngày đến IPFS

IPFS là gì? Định nghĩa từ trang chủ 

> IPFS is the Distributed Web. <br>
> A peer-to-peer hypermedia protocol to make the web faster, safer, and more open.

Vắn tắt, 1 tool xây dựng hệ thống web phân tán, giúp web bạn tồn tại vĩnh viễn, máy chủ của bạn die, web bạn vẫn sống. 

Chi tiết và Cài đặt liên quan, tham khảo tại https://ipfs.io/docs/install/

Sau khi cài đặt, cần khởi động ipfs để tạo ra 1 node trong mạng lưới

```
ipfs daemon
```

Trên terminal khác, có thể chạy lệnh sau để xác nhận xem hiện tại có thể có bao nhiêu peers sẽ share content mà mình sẽ deploy

```
ipfs swarm peers
```

Vì tất cả source code của mình đặt trong thư mục `/src` nên mình sẽ add `/src` này lên IPFS

```
ipfs add -r src/
```

This will add your dist folder to the network. You’ll see a long hash that’s been generated for you. The last hash is a unique identifier for that folder:

Nó sẽ add `src` lên mạng lưới với rất nhiều chuỗi Hash. Chuỗi hash cuối cùng là định danh thư mục `src` của bạn.

```
added Qmb3fJpXVGvUnNeRLC3P5sTXMzjpf5zq4tKt9XjhtYFf1k src/fonts
added QmZP2xGY12cC2h7ef4L3K4UdgysUxbhqQwRedSaX9gX4cn src/images
added QmaRjCe3twqVdVHWgBiPBDAvPKfRPCwwy2d9bNgjDgAifr src/js
added QmRyNG9gMfKZUy6HjTJthyQgqpQ7M7YR56DYhn7BtMP8gC src/jsmaps
added QmfU5NcL3To1k7MVKvf2uswhpbpF8FGyeo6h5JrGZu4qHP src/maps
added QmZVYrxQZgcVqy6kkq7s547kov3RMQ9fXRaHtrJAbd9rvi src
```

Copy hash cuối cùng kia và thực hiện

```
ipfs name publish QmZVYrxQZgcVqy6kkq7s547kov3RMQ9fXRaHtrJAbd9rvi
```

Ta sẽ nhận được kết quả trả về trên Terminal như sau:

```
Published to QmY2E2Yb3dNSkToo7VjooSfDUsnZRqaC9NYAgkiH8G22zG: /ipfs/QmZVYrxQZgcVqy6kkq7s547kov3RMQ9fXRaHtrJAbd9rvi
```

Say oh yeah, content của bạn đã lên sóng. Có thể truy cập để dùng ngay và luôn
https://gateway.ipfs.io/ipns/QmY2E2Yb3dNSkToo7VjooSfDUsnZRqaC9NYAgkiH8G22zG/

Khi có sự thay đổi ở thư mục `src` thì ta sẽ cần làm các thao tác `ipfs add -r src/` và `ipfs name publish ...` lại từ đầu.

Có thể kiếm tra số lượng peers đang kết nối tại 

http://localhost:5001/ipfs/QmQLXHs7K98JNQdWrBB2cQLJahPhmupbDjRuH1b9ibmwVa/#/connections

![_config.yml]({{ site.baseurl }}/images/connected-peers.png)

## 5. Dùng thử DApp xem thế nào

- Thời gian load lần đầu tiên quả là thảm hoạ, ae ai từng download torrent thì biết, có thể do phụ thuộc vào lượng peer kết nối đến nữa. Lượng peers hiện tại trên IPFS là không nhiều
- Với các thao tác với blockchain, các hàm get lấy thông tin rất nhanh, tuy nhiên các hàm Write vào Blockchain thì khá chậm. 

> ## Tổng kết

- Tìm hiểu các quy trình để develop 1 DApp from scratch trên Ethereum Network
- Các thao tác hiện tại với Blockchain tốc độ không được tốt lắm nếu so với truy vấn từ Centralize System
- Code chán chê rồi thì nhận ra trong TH này, game sẽ reset nếu ta deploy 1 contract khác lên smart contract, cái này là do lỗi thiết kế ban đầu. => Smart contract là bất biến trên blockchain, việc thay đổi smart contract cũng giống như reset game, cần thiết kế hợp lý ngay từ bước ban đầu. 
- Smart contract cần được code ngắn gọn và trong sáng. Ngoài việc dễ hiểu, còn ảnh hưởng đến chi phí Gas khi thực thi contract nữa.

