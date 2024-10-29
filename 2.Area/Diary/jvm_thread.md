thread는 daemon thread, normal thread로 두 종류가 있다.

jvm이 시작되면 생성되는 모든 thread는 main thread를 제외하고 모두 daemon thread이다. (역으로 main thread는 normal thread라고 볼 수 있고)

thread는 해당 thread를 생성한 thread의 상태를 상속받으므로 main thread가 만드는 thread는 모두 normal thread이다.

daemon thread는 normal thread의 작업을 돕는 보조적 역할을 담당하는 thread이다.

보조적 역할로는 garbage collection , request 처리, resource cleanup과 같은 background task를 실행하며 낮은 우선순위를 갖고
normal thread가 실행중인 상태에서만 동작하기 때문에 normal thread가 종료되면 daemon thread는 강제 종료된다.