# 테스트 코드 



## 테스트 코드를 사용하는 이유

> 우리가 작성한 코드들에 대해 수시로 빠르게 검증을 받을 수 있으며, 유지보수 및 리팩토링을 진행 할 때에도 안정성을 확보한 채 진행할 수 있다는 장점이 있기 때문

*** 무엇보다도 테스팅에 대한 리소스 절감이 가장 큰 이유 *** 

postman이나 swagger로 직접 API를 돌리면서 테스트하는 것보다 훨씬 효율적이다.



## <span style="color: red">But,</span> 

테스트코드의 실행에 너무 많은 시간이 소요되면 테스트 코드의 효율성이 떨어질뿐만 아니라, 리팩토링 진행 시에 너무 많은 리소스를 투자하게 된다는 단점이 생긴다. 

> 이를 해결하기 위해 최적화를 진행해 보자!!



## 최적화 방법

* @DirtiesContext 를 적제적소에만 사용하기

  * @DirtiesContext란? : 스프링 테스트 컨텍스트 프레임워크에 해당 클래스에서 컨텍스트 상태를 변경하는것을 알려주는 역할이다. (제대로 공부 안하고 사용할 경우 설정을 알맞게 초기화 시켜서 테스트 하기 편하게 하기 위해 남발하는 경우가 있음)
  * 하지만 @DirtiesContext를 쓰면 결국 테스트마다 새로운 어플리케이션을 로드해야 한다. => 이는 매우 큰 비용을 야기한다.
  * 스프링에선 어플리케이션 컨텍스트 캐싱기능을 제공해 동일한 컨텍스트 사용시에는 자동을 ㅗ로드해주는 기술을 적용함
  * 따라서, 이러한 이점을 버리지 말고 설정이 바뀌어야하는 경우에만 DirtiesContext 어노테이션을 사용하자!

* Application Context 캐싱 조건 파악하기

  * Aplication Context의 캐싱 기능을 사용하더라도 처음 사용되는 설정들에 대해서는 새로 생성해주는 시간 소요가 발생됨
  * 공통 설정파일을 빼 각 테스트 클래스에 상속시켜보자! => 특정 객체를 @MockBean 즉 mocking을 시켜버리면 서로 다른 설정을 가졌다고 간주, 새로운 컨텍스트를 생성함 
  * 따라서, 모든 mock class들과 설정 어노테이션들을 빼서 추상 클래스를 만들고 이를 각 테스트 클래스들에 상속시키면 새로운 컨텍스트를 생성하지 않고 진행됨 (시간 단축)

* 실제 서버를 구동시켜 Http 요청을 보내면서 진행하는 테스트

  * 해당 테스트는 실제 서버가 구동되면서 진행되는 테스트기 때문에 비교적 부하가 심하고 네트워크 딜레이로 인해 시간 소요가 더 심하다

  * 일반적으론 Mockito와 같은 mocking을 이용하여 시간을 줄인다.

    * ```Java
      @Test
          @DisplayName("챌린지 삭제 시, 챌린지 아이디로 챌린지를 못찾으면 404 에러 테스트")
          public void testIfDeleteChallengeIsNullNotFoundErr() {
      
              //given
              ChallengeCategoryRepository mockChallengeCategoryRepository = Mockito.mock(
                  ChallengeCategoryRepository.class);
              TargetItemRepository mockTargetItemRepository = Mockito.mock(TargetItemRepository.class);
              ChallengeRepository mockChallengeRepository = Mockito.mock(ChallengeRepository.class);
              ChallengeUserRepository mockChallengeUserRepository = Mockito.mock(
                  ChallengeUserRepository.class);
              ProgressRepository mockProgressRepository = Mockito.mock(ProgressRepository.class);
              FamilyUserRepository mockFamilyUserRepository = Mockito.mock(FamilyUserRepository.class);
              KidRepository mockKidRepository = Mockito.mock(KidRepository.class);
              CommentRepository mockCommentRepository = Mockito.mock(CommentRepository.class);
      
              ChallengeRequest challengeRequest = new ChallengeRequest(true, "이자율 받기", "전자제품", "에어팟 사기",
                  30L,
                  150000L, 10000L, 15L);
      
              User newUser1 = User.builder().id(1L).username("user1").isFemale(true).birthday("19990521")
                  .authenticationCode("code").provider("kakao").isKid(true).refreshToken("token").build();
      
              User newParent = User.builder().id(2L).username("parent1").isFemale(true)
                  .birthday("19990521")
                  .authenticationCode("code").provider("kakao").isKid(false).refreshToken("token")
                  .build();
      
              ChallengeCategory newChallengeCategory = ChallengeCategory.builder().id(1L)
                  .category("이자율 받기").build();
      
              TargetItem newTargetItem = TargetItem.builder().id(1L).name("전자제품").build();
      
              Challenge newChallenge = Challenge.builder().title(challengeRequest.getTitle())
                  .contractUser(newParent)
                  .isAchieved(1L).totalPrice(challengeRequest.getTotalPrice())
                  .weekPrice(challengeRequest.getWeekPrice()).weeks(challengeRequest.getWeeks())
                  .challengeCategory(newChallengeCategory).targetItem(newTargetItem).status(1L)
                  .interestRate(challengeRequest.getInterestRate()).build();
      
              ChallengeUser newChallengeUser = ChallengeUser.builder().challenge(newChallenge)
                  .member("parent").user(newUser1).build();
      
              Progress newProgress = Progress.builder().id(1L).weeks(1L).isAchieved(true)
                  .challenge(newChallenge).build();
      
              Progress newProgress2 = Progress.builder().id(2L).weeks(2L).isAchieved(true)
                  .challenge(newChallenge).build();
      
              Mockito.when(mockChallengeUserRepository.findByChallengeId(newChallenge.getId()))
                  .thenReturn(Optional.ofNullable(newChallengeUser));
      
              //when
              ChallengeServiceImpl challengeService = new ChallengeServiceImpl(mockChallengeRepository,
                  mockChallengeCategoryRepository, mockTargetItemRepository, mockChallengeUserRepository,
                  mockProgressRepository, mockFamilyUserRepository, mockCommentRepository,
                  mockKidRepository);
              ChallengeController challengeController = new ChallengeController(challengeService);
              Long challengeId = newChallenge.getId();
      
              //then
              Assertions.assertThrows(NotFoundException.class, () -> {
                  challengeController.deleteChallenge(newUser1, 2L);
              });
          }
      ```

  * fixture 환경을 post 요청으로 구성하지 말고 재활용하자!

    * 단, fixture 환경은 변경되면 안된다. (Because 테스트들은 독립적이어야 하기 때문)
    * fixture 환경을 변화시킬 수 있는 CUD 요청의 경우와 변화를 안시키는 Read 요청의 경우 DB를 분리 시켜서 따로 사용한다.
    * 각 DB에 더미데이터들을 박아놓고 사용 => CUD의 경우는 LazyConnectionDataSourceProxy를 이용해 필요한 시점에만 가져오는 방향으로 사용하면 환경을 재활용함으로써 시간을 단축 할 수 있다.

  * 하지만, 새로운 도메인 추가시에는 Read DB를 위한 환경을 새롭게 그리고 조심히 구성해야함



### 결론: 유지보수와 리팩토링을 빠르고 안전하게 진행하기 위해 테스트 코드 최적화를 진행하자!

