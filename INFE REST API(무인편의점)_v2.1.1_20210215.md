# INFE REST API v2.1.1

2021-02-15

### API  명세

| No | API                                    | Description             | 방향           |
|----|----------------------------------------|-------------------------|-------------------------|
| 1  | https://*/infe/auth/login/{SVCNAME} | 시스템 접속인증(미사용)  | INFE(지능망) |
| 2  | https://*/infe/auth/refreshToken | 시스템 상태확인(미사용)    | INFE(지능망)             |
| 3 | https://*/selfShop/auth | 시스템 접속인증 | INFE(지능망)->무인점포 시스템 |
| 4 | https://*/selfShop/refreshToken | 접속정보 갱신 | INFE(지능망)->무인점포 시스템 |
| 5  | https://*/selfShop/wakeUp | 개폐 요청 | INFE(지능망)->무인점포 시스템 |

***

### [POST/JSON] https://*/infe/auth/login/{SVCNAME}

##### 시스템 접속 인증(미사용)

- 등록된 IP{+MAC} 주소만 token을 발급
- 재호출 시 접속 토큰 업데이트
- SVCNAME = selfShop

| 필드    | 구분 | 타입        | 필수유무 | 설명                                          | 비고            |
| ------- | ---- | ----------- | -------- | --------------------------------------------- | --------------- |
| IpAddr  | IN   | String[20]  | M        | 접속 시스템 IP 주소                           | ex)127.0.0.1    |
| MacAddr | IN   | String[20]  | O        | 접속 시스템 mac 주소<br />없을 경우 접속 허용 | ex)AABBCCDDEEFF |
| SysId   | IN   | String[20]  | M        | 시스템 인증용 비교 문자열                     | ex)1234567      |
| Result  | OUT  | Integer     | M        |                                               |                 |
| Token   | OUT  | String[128] | M        |                                               |                 |

> Request Example <br>

```json
{
  "IpAddr":"127.0.0.1",
  "MacAddr":"AABBCCEEFF",
  "SysId":"KT-IDC"
}
```

> Response Example<br>
> 성공실패 여부를 리턴한다. result 가 성공=0 이외에 파라미터가 잘못 된 경우<br>
> 코드번호 -1 또는 그 밖에 상단에 정의된 에러코드로 응답 될 수 있음(에러코드 정의 참고)

```json
{
  "Result":0,
  "Token":"Q29uc3V..."
}
```

***

### [POST/JSON] https://*/infe/auth/refreshToken

##### 시스템 상태 확인(미사용)

- 모든 API 요청 시 Token 만료 시간 업데이트(최소 2시간 이상)
- 요청 주기는 30초 이상

| 필드   | 구분 | 타입        | 필수유무 | 설명                    | 비고 |
| ------ | ---- | ----------- | -------- | ----------------------- | ---- |
| Token  | IN   | String[128] | M        |                         |      |
| UserId | IN   | String[20]  | O        |                         |      |
| Result | OUT  | Integer     | M        | 성공=0, 실패=0보다 작음 |      |
| Token  | OUT  | String[128] | M        |                         |      |

> Request Example<br>
> 모든 API 요청 시 Token 시간 업데이트 한다.

```json
{
  "Token":"Q29uc3V..."
}
```

> Response Example<br>
> Token 시간 업데이트 결과를 상태, 접속 호스트, 결과 설명 정보로 리턴 한다.

```json
{
  "Result":0,
  "Token":"Q29uc3V..."
}
```

***
### [POST/JSON] https://*/selfShop/auth

##### 시스템 접속 인증

- 등록된 IP 주소와 SysId만 접속 token을 발급 (요청정보에 MacAddr이 있을 경우 등록여부 확인)
- 이미 접속 Token이 발급되었고 만료되지 않은 상태이면 Token 업데이트
- 접속 Token은 2시간 후 만료

| 필드    | 구분 | 타입        | 필수유무 | 설명                                          | 비고            |
| ------- | ---- | ----------- | -------- | --------------------------------------------- | --------------- |
| IpAddr  | IN   | String[20]  | M        | 접속 시스템 IP 주소                           | ex)127.0.0.1    |
| MacAddr | IN   | String[20]  | O        | 접속 시스템 mac 주소<br />없을 경우 접속 허용 | ex)AABBCCDDEEFF |
| SysId   | IN   | String[20]  | M        | 시스템 인증용 비교 문자열                     | ex)1234567      |
| Result  | OUT  | Integer     | M        | 성공=0, 실패=0보다 작음                       |                 |
| Token   | OUT  | String[128] | M        |                                               |                 |

> Request Example <br>

```json
{
  "IpAddr":"127.0.0.1",
  "SysId":"KT-INFE"
}
```

> Response Example<br>
> 성공실패 여부를 리턴한다. result 가 성공=0 이외에 파라미터가 잘못 된 경우<br>
> 코드번호 -1 또는 그 밖에 상단에 정의된 에러코드로 응답 될 수 있음(에러코드 정의 참고)

```json
{
  "Result":0,
  "Token":"Q29uc3V..."
}
```
***
### [POST/JSON] https://*/selfShop/refreshToken

##### 시스템 상태 확인

- 모든 API 요청 시 Token 만료 시간 업데이트(최소 2시간 이상)
- 요청 주기는 30초 이상

| 필드   | 구분 | 타입        | 필수유무 | 설명                                                         | 비고 |
| ------ | ---- | ----------- | -------- | ------------------------------------------------------------ | ---- |
| Token  | IN   | String[128] | M        | token 확인<br />접속 token이 만료된 상태이면 Result -401 응답 |      |
| Result | OUT  | Integer     | M        | 성공=0, 실패=0보다 작음                                      |      |

> Request Example <br>

```json
{
  "Token":"Q29uc3V..."
}
```

> Response Example<br>
> 성공실패 여부를 리턴한다. result 가 성공=0 이외에 파라미터가 잘못 된 경우<br>
> 코드번호 -1 또는 그 밖에 상단에 정의된 에러코드로 응답 될 수 있음(에러코드 정의 참고)

```json
{
  "Result":0
}
```
***

### [POST/JSON] https://*/selfShop/wakeUp

##### wakeUp 요청

- 요청시 마다 Token 만료 시간 업데이트

| 필드         | 구분 | 타입         | 필수유무 | 설명                                                         | 비고 |
| ------------ | ---- | ------------ | -------- | ------------------------------------------------------------ | ---- |
| Token        | IN   | String[128]  | M        | token 확인<br />접속 token이 만료된 상태이면 Result -401 응답 |      |
| CallingNb    | IN   | String[20]   | M        | 발신번호 ex) 010OOOOOOOO                                     |      |
| SubNb        | IN   | String[20]   | M        | 가입자번호 ex) 080OOOOOOO                                    |      |
| result       | OUT  | Integer      | M        | 성공=0, 실패=0보다 작음<br />-401 : Token 만료 또는 잘 못 된 Token 인증 필요 |      |
| ShopId       | OUT  | String[64]   | M        | Shop 식별자                                                  |      |
| ShopName     | OUT  | String[64]   | M        | Shop 명칭                                                    |      |
| ShopLocation | OUT  | Double Array | O        | 경, 위도                                                     |      |
| ShopAddress  | OUT  | String[128]  | O        | 주소                                                         |      |

> Request Example <br>

```json
{
  "Token":"Q29uc3V...",
  "CallingNb":"01073331031",
  "SubNb":"0800000000"
}
```

> Response Example<br>
> 성공실패 여부를 리턴한다. result 가 성공=0 이외에 파라미터가 잘못 된 경우<br>
> 코드번호 -1 또는 그 밖에 상단에 정의된 에러코드로 응답 될 수 있음(에러코드 정의 참고)
> ShopName을 포함하여 콜체크인 완료 문자 발송

```json
{
  "Result":0,
  "ShopId":"ABCD1234",
  "ShopName":"동대구역점",
  "ShopLocation":[127.0317674,37.6658609],
  "ShopAddress":"대구광역시 동구 신암동 동대구로 550"  
}
```



