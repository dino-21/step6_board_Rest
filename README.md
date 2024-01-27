
![12](https://github.com/dino-21/step6_board_Rest/assets/80396471/e4c5f318-e53a-4dc5-874a-d18be58ccce2)

![13](https://github.com/dino-21/step6_board_Rest/assets/80396471/fc2896a4-5175-46d1-822d-173bdcba2ca0)

![17](https://github.com/dino-21/step6_board_Rest/assets/80396471/c25da4f6-c971-42c7-b863-5814d36fbbed)


1 Rest 프로젝트를 위한
   스프링 프로젝트만들기

2 pom.xml  라이브러리 설정 
jackson-databind : 객체를 JSON 변환할 때 사용
jackson dataformat xml : 객체를 XML로 변환할 때 사용
gson : 테스트용으로 직접 Java객체를 JSON 타입의 문자열로 변환하기 위해 사용


3 @RestController
    //URL 매핑           //리턴결과
  @GetMapping(value = "/getText", produces = "text/plain; charset=UTF-8")


4 객체의 반환
객체를 반환하는 작업은 JSON이나 XML을 이용한다. 전달된 객체를 생산하기 위해서 
org.zerock.domain 패키지를 생성하고 SampleVO 클래스를 작성한다.
SampleController에서 SampleVO를 리턴하는메서드를 설계한다.

5 @GetMapping(value = "/getSample",
		produces = { MediaType.APPLICATION_JSON_VALUE,
			     MediaType.APPLICATION_XML_VALUE})

6  @RestController 대신 @Controller + @ResponseBody사용해 보기

7  xml이 json보다 우선순위    MediaType.APPLICATION_XML_VALUE 지우기

8  @GetMapping이나 @RequestMapping의 produces 속성은 생략가능하다.


9  컬렉션 타입의 객체 반환

@GetMapping(value = "/getList")
public List<SampleVO> getList() {

	//        10번 반복(1~10)                i전달되면  10개  객체생성 리스트생성
	return IntStream.range(1, 10).mapToObj(i -> new SampleVO(i, i + "First", i + " Last"))
			.collect(Collectors.toList()); //리스트생성

}
	

10 ResponseEntity타입  - 상태코드 값도 같이 
@GetMapping(value = "/check", params = { "height", "weight" })
public  ResponseEntity<SampleVO> check(Double height, Double weight) {

	SampleVO vo = new SampleVO(000, "" + height, "" + weight);

	ResponseEntity<SampleVO> result = null;

	if (height < 150) {
		result = ResponseEntity.status(HttpStatus.BAD_GATEWAY).body(vo);
	} else {
		result = ResponseEntity.status(HttpStatus.OK).body(vo);
	}

	return result;
}
	


11 @RestController에서 파라미터

@PathVariable
@GetMapping("/product/{cat}/{pid}")
public String[] getPath(@PathVariable("cat") String cat, @PathVariable("pid") Integer pid) {

	return new String[] { "category: " + cat, "productid: " + pid };
}
           http://localhost:8081/sample/product/kangsan/5
          url에서 값을 받아서 cat변수에 대입시켜줌, url에서 값을 받아 변수 pid에 대입
public String[] getPath(@PathVariable("cat") String cat, @PathVariable("pid") Integer pid) { }

12  @RequestBody
 HttpMessageConverter타입의 객체들을 이용해서 다양한 포맷의 입력 데이터를 변환할 수 있다.

Ticket.java
package org.zerock.domain;

import lombok.Data;

@Data
public class Ticket {
	private Integer tno;
	private String owner;
	private String grade;
}




13 Ticket을 사용하는 예제 SampleController에 추가한다.

@PostMapping("/ticket")  //json - >java객체로..
public Ticket convert(@RequestBody Ticket ticket) {

	log.info("convert.......ticket" + ticket);

	return ticket;

}


14 REST 방식의 테스트
위와 같이 GET 방식이 아니고 POST 등의 방식으로 지정되어 있으면서 JSON 형태의 데이터를 처리하는 것을 브라우저에서 개발하려면 많은 시간과 노력이 들어간다.
@RestController를 쉽게 테스트할 수 있는 방법은 주로 REST방식의 데이터를 전송하는 툴을 이용하거나 
JUnit과 spring-test를 이용해서 테스트하는 방식을 고려할 수 있다.

JUnit 기반의 테스트



15 SampleControllerTests.java   - 가짜 서버 목업으로 작업하기

16 다양한 전송방식
HTTP의  전송방식
POST
GET
PUT
DELETE
