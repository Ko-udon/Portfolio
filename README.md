https://github.com/WishMarket/WishMarket-BE

[Gift4U (Gift for you)](https://www.notion.so/Gift4U-Gift-for-you-f1afd19acafc44e6b93ac3d661a32182) (팀 작업 페이지)

[[Demo Day] Gift4U](https://www.notion.so/Demo-Day-Gift4U-c68d3847d43245ef98b94aeebebc433a) (발표자료)

[https://wishmarket.netlify.app/](https://wishmarket.netlify.app/) (사이트)

![image](https://user-images.githubusercontent.com/79897135/235173030-c33f80b0-8cc8-465b-8957-4a83a13ddeb2.png)

- 주고싶거나 받고싶은 선물이 있을때, 고민없이 위시마켓
- 개발 인원 :  6명 (백엔드4 + 프론트2)
- 역할 : 백엔드 개발
- 개발 기간 : 2023.01.26 ~ 2023.03.09
- 성과 및 결과 :
    - 프론트엔드와 직접적인 협업과 다양한 백엔드 기술을 활용
    - AWS EC2, AWS RDS, AWS S3, Docker를 활용한 배포
    - Github Action을 통한 CI, CD 구축
    - Spring Batch, Spring Scheduler 활용한 주기적 데이터 처리
    - Redis를 활용한 데이터 캐시화

### 데모영상

[https://youtu.be/IbfWnYwOlSs](https://youtu.be/IbfWnYwOlSs)

https://file.notion.so/f/s/1ccce581-0adb-4922-96af-f7f5a88b2c48/Untitled.png?id=42dd178d-aabf-4abc-8c7e-ea6132201c04&table=block&spaceId=c92132b9-3e40-4ee2-b587-4b29c628f820&expirationTimestamp=1682777956689&signature=RCBtvDbMYSIZcVFAtit5oOjnbVWe-5XXMPsJ5X2bpqo&downloadName=Untitled.png

### 설계 / 개발

**사용 기술** 

- Back-end :
    - `Java11, OpenJDK17.0.2`
    - `Spring Boot, Spring Batch, Spring Security, Spring Data JPA`
    - `Querydsl, Hibernate`
    - `Junit5, Mockito`
    - `Gradle`
    - `AWS EC2, RDS, S3, GithubAction`
    - `IntelliJ`
- Database : `MariaDB, Redis, H2`

**ERD** 

[Team G4U](https://www.erdcloud.com/d/6DmtMZZyuhs3ZZNaj)

![image](https://user-images.githubusercontent.com/79897135/235173170-2667d12c-a54c-4f7f-8eee-a643b420f3e0.png)

- 주요 기능 테이블
    - Funding, Product, WishList 테이블
    - 펀딩은 펀딩 상품을 FK로 가지고 있으며 받을 대상인 유저에 대한 정보, 펀딩 기간, 금액 정보들을 가지고 있습니다.
    - 사용자가 찜목록(WishList)에 담은 상품을 바로 펀딩을 생성할 수 있습니다.

# My work

**Database** 

- MariaDB, H2, Redis
- 베스트 상품 캐시화
    - 상품의 isBest컬럼을 통해 베스트 상품 여부를 판단합니다.
    - 메인 페이지에 베스트 상품 목록을 보여줍니다.
    - 다수의 사용자들이 매번 메인 페이지에 접속해 그때마다 상품이라는 대용량 데이터에 접근해 일일히 isBest값을 조회하여 베스트 상품을 반환하는 것은 비효율적이고, 쿼리 조회가 너무 빈번하게 발생했습니다.
        - 해결
            - 베스트 상품을 매번 조회하지 않고, 그날 최초의 조회 한번을 통해 베스트 상품목록에 대한 데이터를 Redis에 저장
            - 이후 다른 유저들이 베스트 상품을 조회하면 데이터베이스에 직접 접근하지 않고 레디스에 저장된 값을 읽어오도록 수정

**Server**

- 많은 수의 데이터를 일정 시간마다 다루어야 하는 기능의 경우 처음에 단순히 스프링 스케줄러를 활용했지만 프로젝트 초기 단계에선 데이터 수가 적어 괜찮았지만, 실제 서비스를 운영하는 단에선 대용량 데이터를 고려해야 했습니다.
    - 많은 수의 데이터를 처리해야하는 과정에서 일괄처리(배치)를 알게됨
    - 사용자와 상호작용 없이, 여러개의 작업을 미리 정해진 순서에 따라 ‘중단’ 없이 처리하는 특징을 가짐
    - 신뢰성, 성능, 자동화, 대용량 데이터에 적절함을 알게됨
    - 데이터 처리 과정을 배치에 등록 후, 스케줄러를 사용해 배치 작업을 실행하여 해결

### 내가 구현한 기능 목록

- 정해진 시간에 베스트 상품 업데이트, 펀딩 만료 판단
    - 스프링 배치, 스케줄러 기능을 활용하여 매일 새벽 2시에 베스트 상품 목록을 업데이트
    - 매 정각마다 진행중인 펀딩들의 기간만료여부를 파악해 펀딩 실패 여부 업데이트
    
- 사용자 찜목록 기능
    - 찜목록 추가, 조회, 삭제
    - 특정 유저의 찜목록 조회 기능
    
![image](https://user-images.githubusercontent.com/79897135/235173229-e0e052b6-1671-4595-bc2d-754c1747b561.png)
    
- 다양한 상품 조회 기능
    - 카테고리별 상품 조회
    
![image](https://user-images.githubusercontent.com/79897135/235173333-4b09ece6-61f5-49da-95df-bd456525d128.png)
    
    - 베스트 상품 조회
    
![image](https://user-images.githubusercontent.com/79897135/235173392-80c3caf6-506e-4d80-88ee-7786f63a55c8.png)
    

- 펀딩 기능 구현
    - 펀딩 시작하기, 참여하기(이미 진행중인 펀딩에 대해서), 상세 정보 조회 등 기본적인 기능 구현
        
![image](https://user-images.githubusercontent.com/79897135/235173465-df2c3684-9d64-4426-b396-6c119c2f7709.png)
        
![image](https://user-images.githubusercontent.com/79897135/235173513-cb04b6af-043f-4680-af62-905732d51003.png)
        
    
    - 특정 유저에 대한 무작위 펀딩 내역 조회
        - 몇몇 유저들은 인기유저(인플루언서)로 구분하여 해당 유저들을 대상으로 진행중인 펀딩을 조회시 매번 랜덤한 결과값을 반환함
        
![image](https://user-images.githubusercontent.com/79897135/235173552-2c00261f-1406-4e1f-a0c4-54f311d55c0b.png)
        
    

- 간단한 관리자 페이지
    - 상품 데이터를 db에 일일이 넣기에는 다소 무리가 있다고 생각
    - 상품 데이터를 직접 넣는 기능과 관리자 페이지를 추가, 작업 효율성 상승
        
![image](https://user-images.githubusercontent.com/79897135/235173614-e93be201-49dd-435e-bb6b-5a9052b659d8.png)
        

### TroubleShooting

- **날짜 데이터를 받아오는 값이 9시간 차이나게끔 넘어오는 현상**
    
![image](https://user-images.githubusercontent.com/79897135/235173680-cd587315-b3bb-4c07-a79f-3c017dcd8398.png)
    
    - **이슈**
        - 새로운 펀딩을 등록시 저장되는 데이터의 날짜가 9시간 이전 시간으로 저장이 되는 현상
        - 펀딩 만료날짜가 잘못 저장되므로 큰 문제가 됨
    - **해결**
        - 프론트에서 전달하는 Json format 형식을 “YYYY-MM-dd HH:mm” 으로 포멧
        - 백엔드에서 전달받는 InputForm에 @JsonFormat 형식 지정, 입력받는 시간에 **우리나라 시간은 UTC보다 09:00 빠르므로 timezone을 지정하여 parsing**

- 베스트 상품 기능
    
![image](https://user-images.githubusercontent.com/79897135/235173715-a0f6410e-6229-4e9d-8ed1-b25e1c5b2afd.png)
    
![image](https://user-images.githubusercontent.com/79897135/235173758-b22337da-a785-4319-9019-d4949330a1e8.png)
    
![image](https://user-images.githubusercontent.com/79897135/235173786-679919b6-3b76-4b6e-acdc-5be9afd087b6.png)
    
    - **이슈**
        - 베스트 상품 목록을 메인 홈페이지에 표출하여 동시에 많은 유저가 접속 시 DB에 직접적인 접근과 무거운 연산으로 인한 부하 발생
        - 베스트 상품을 유저가 매번 접속할때마다 연산을 통해 검색하는 일은 비효율적
    - **해결**
        - 매일 자정 SpringBatch, Scheduler 라이브러리를 활용해 베스트 상품목록을 미리 업데이트, 캐시화 하여 저장
        - 매번 같은 결과값에 대한 데이터 요청을 캐시화를 통해 직접적인 연산을 회피하여 효율을 높임

## 프로젝트를 마무리 하며

 제가 구현한 대표적인 기능으로 상품의 추천 수를 기반으로 베스트 상품을 TOP 50개를 선정하여 메인 홈페이지에 표시해주는 기능이 있습니다. 하지만 성능 테스트를 위해 동시에 많은 수의 메인 홈페이지 요청을 시도해보니 버벅거리는 현상과 서버 요청이 지연되는 현상이 발생했습니다. 로그를 분석해보니 다른 부분보다 베스트 상품을 DB에서 검색하는 데에 많은 부하가 걸려 해당 현상이 일어났음을 알게 되었습니다. 

 원인 파악 후 현재 환경에선 실시간으로 무수히 많은 데이터를 비교해가며 검색하기에는 무리가 있지만 베스트 상품에 대한 데이터 처리는 필요하므로 이를 미리 연산하여 따로 저장해두면, 즉 캐시 화를 해두면 접속할 때마다 일일이 연산할 필요가 없지 않을까 하는 생각하였습니다. 해당 문제에 대해 협의를 통해 팀원들 역시 메인 홈페이지에 이동할 때마다 추천 수를 비교하여 상품을 조회하는 일은 비효율적이라고 동의하였고, 베스트 상품은 매일 자정 시간에 TOP 50개를 선정해 업데이트하고 캐시화 하여 DB에 직접 조회하지 않도록 처리할 수 있었습니다. 

 한 가지 아쉬운 점은 원래 의도했던 실시간 베스트 상품으로 처리하지 못했던 점과 상품 처리를 위한 별도의 DB를 확장하여 처리해보는 것도 가능했을 것 같다는 아쉬움이 있었습니다. 또한 실제 서비스에서 베스트 상품을 선별하는 기준은 단순 추천 수를 기준으로 판별하기보단 사용자들의 해당 상품 클릭 횟수, 관심도, 검색 비중 등 다양한 방식이 있기에 단순한 선정 방식에 조금 아쉬움이 남는 경험이였습니다.
