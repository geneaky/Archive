@RequestParam을 명시적으로 사용할 경우 내부적으로 require=true가 기본값이지만 생략하는 경우 false로 전환이된다. 

이 애너테이션 사용대신 optional 파라미터로 받을지 말지에 대한 고민을 했었는데 결국 최상위 layer인 controller에서 optional을 받게되면 그 파라미터를 전달받아 사용하게되는 하단부 layer까지 단순 null체크 후 값을 가져오는 optional이 퍼지게되는데 optional을 사용할때는
[[Optional 사용에 대한 고민]]  여기서 말하는 의도에 맞춰 사용해야하므로 곧바로 값을 받아서 nullable한 파라미터를 사용하는 곳에서 null체크를 하고 사용하는 방식이 더 깔끔하다는 생각이 들었다.
