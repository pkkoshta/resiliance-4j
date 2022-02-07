# resiliance-4j

1. add the 3 dependency 
   a. spring aop
   b. spring actuator
   c. resiliance4j-springboot2
2. create a new method in controller that will execute once perticular api or service is down.
3.  place @CricutBreaker annotation on the top of the actual api
4.  example : @CircuitBreaker(name =USER_SERVICE,fallbackMethod = "getAllProductWhenCategoryDown")

@GetMapping
	@CircuitBreaker(name =USER_SERVICE,fallbackMethod = "getAllProductWhenCategoryDown")
	public List<Product> products(@RequestParam("category") String category){
    String url = category == null ? URL : URL + "/" + category;
      return  restTemplate.getForObject(url, ArrayList.class);
	}

	public List <Product> getAllProductWhenCategoryDown(Exception e){
		List <Product> products = Arrays.asList(
				new Product(1, "Ear",  "electronics", 5.0f, 10),
				new Product(2, "Mobile",  "electronics", 5.0f, 10),
				new Product(3, "Sofha",  "furniture", 5.0f, 10),
				new Product(4, "Mobile",  "electronics", 5.0f, 10)
		);
		return products;
	}

6.  add following properties in application.yaml file
application.yaml
----------------
management:
  health:
    circuitbreakers:
      enabled: true
  endpoints:
    web:
      exposure:
        include: health
  endpoint:
    health:
      show-details: always


resilience4j:
  circuitbreaker:
    instances:
      userService:
        registerHealthIndicator: true
        eventConsumerBufferSize: 10
        failureRateThreshold: 50
        minimumNumberOfCalls: 5
        automaticTransitionFromOpenToHalfOpenEnabled: true
        waitDurationInOpenState: 5s
        permittedNumberOfCallsInHalfOpenState: 3
        slidingWindowSize: 10
        slidingWindowType: COUNT_BASED
