개발자를 위한 쉬운 도커
박태웅의 AI 강의 2026
SQL, 이렇게 하면 된다
AWS로 배우는 실전형 CI/CD : 따라하며 완성하는 클라우드 배포 입문서
미니멀리즘 프로그래머 : AI 시대, 복잡함을 줄이고 가치를 올리는 개발 원칙
클로드 코드·코덱스 CLI ·제미나이 CLI 완전 활용법 : 한 권으로 끝내는 AI 터미널 도구 3대장 바이브 코딩
모던 자바 동시성 프로그래밍  : 편리함, 견고함, 고성능, 어느 것도 양보하지 않는 새로운 자바의 표

모니터링의 새로운 미래 관측 가능성 : 프로메테우스, 그라파나, 오픈텔레메트리까지 마이크로서비스와 인공지능 중심의 옵저버빌리티 구현

이벤트 소싱과 마이크로서비스 아키텍처 
클라우드 애플리케이션 아키텍처 패턴


그린 소프트웨어 : 지속 가능한 소프트웨어 개발과 운영 = Building green software
도서. (스프링 6와 스프링 부트 3로 배우는) 모던 API 개발 : Java 17과 Spring Boot 3 기반의 REST, gRPC, GraphQL을 활용한 반
Vue 3와 스프링 부트로 시작하는 웹 개발 철저 입문 : 스프링 부트와 Vue 3
도서. 스프링 시큐리티 인 액션 : 보안 기초부터 OAuth 2까지, 스프링 시큐리티
모던 자바스크립트 핵심 가이드 : 자바스크립트 기초부터 타임스크립트, ES2021까지 핵심만 쏙쏙

https://rebeccacho.gitbooks.io/java-study-group/content/chapter12.html

https://item.gmarket.co.kr/Item?goodscode=4689768405

https://item.gmarket.co.kr/Item?goodscode=2625086904

https://wikidocs.net/book/19104

package com.psj.exam.template.thymeleaf.component;

@Slf4j
@Component
@RequiredArgsConstructor
public class ThymeleafEmailTemplate {
	private final TemplateEngine htmlTemplate;
	
	/**
	 * 이메일 템플릿(html)에 Thymeleaf를 사용해서 값을 치환 후 리턴합니다.
	 * 
	 * @param htmlFileNm 이메일 템플릿 명(.html 전까지)
	 * @param paramMap 맵핑할 데이터 Map
	 * @return 값이 맵핑된 html 컨텐츠 리턴
	 */
	public String makeEmailContents(String htmlFileNm, Map<String, Object> paramMap) {
		Context ctx = new Context();
		
		for (Map.Entry<String, Object> entry : paramMap.entrySet()) {
			ctx.setVariable(entry.getKey(), entry.getValue());
		}
		
		return htmlTemplate.process(htmlFileNm, ctx);
	}
}

package com.psj.exam.template.thymeleaf.controller;

@Slf4j
@RequiredArgsConstructor
@Controller
@RequestMapping("/thymeleaf")
public class ThymeleafController {
	private final ThymeleafService thymeleafService;
	
	@PostMapping("/simple-template.do")
	public ResponseEntity<?> simpleTemplate(HttpServletRequest request, HttpServletResponse response, @RequestBody Map<String, Object> paramMap) throws Exception {
		return ResponseEntity.ok(thymeleafService.simpleTemplate(request, response, paramMap));
	}
}

package com.psj.exam.template.thymeleaf.service;

@Slf4j
@Service
@RequiredArgsConstructor
public class ThymeleafService {
	private final ThymeleafEmailTemplate template;
	
	/**
	 * html 파일에 subject, content 값을 치환해서 반환합니다.
	 * 
	 * <p>Thymeleaf Template Engine을 사용합니다.</p>
	 * @return 두 정수의 합
	 */
	public String simpleTemplate(HttpServletRequest request, HttpServletResponse response, Map<String, Object> paramMap) throws Exception {
		return template.makeEmailContents(
				"simple-template", 
				Map.of("subject", paramMap.get("subject"), "content", paramMap.get("content"))
		);
	}
}

package com.psj.exam.template.thymeleaf;

@SpringBootTest
@AutoConfigureMockMvc
@Slf4j
public class ThymeleafControllerTest {
	@Autowired
    protected MockMvc mockMvc;

    @Autowired
    private WebApplicationContext context;
    
    @Autowired
    private ObjectMapper objectMapper;
    
    @BeforeEach
    public void mockMvcSetUp() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(context)
                .addFilters(new CharacterEncodingFilter("UTF-8", true))  // 필터 추가
                .build();
    }
    
    @DisplayName("html 파일에 subject, content 값을 치환해서 반환하는 메소드 테스트")
    @Test
    public void simpleTemplate() throws Exception {
        final String url = "/thymeleaf/simple-template.do";
        final String jsonContents = objectMapper.writeValueAsString(SimpleMailDto.builder().subject("제목 입니다.").content("내용 입니다.").build());
        
        log.info("jsonContents : {}", jsonContents);
        
        // when
        final ResultActions resultActions = mockMvc.perform(post(url)
        		.content(
        			objectMapper.writeValueAsString(SimpleMailDto.builder().subject("제목 입니다.").content("내용 입니다.").build())
        		)
        		.contentType(MediaType.APPLICATION_JSON));
        
        String contents = resultActions.andReturn().getResponse().getContentAsString();
        
        log.info("result contents : {}", contents);
    }
}

package com.psj.exam.template.thymeleaf.dto;

@Builder
@Data
public class SimpleMailDto {
	private String subject;
	private String content;
}
