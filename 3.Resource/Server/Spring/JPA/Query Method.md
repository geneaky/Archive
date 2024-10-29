
repository interface에 메서드를 jpa에서 정한 규칙대로 작성했을때 구현을 jpa가 해주는 것인데 
이게 entity내부 필드에 매핑된 연관 entity가 있고 이 entity의 id를 사용해서 query method의 파라미터로 작성하는 경우 쿼리가 날아가는 시점에 left join이 걸리며 fetch join을 진행하게된다.

``` java
public interface ItemTagRepository extends JpaRepository<ItemTag, Long> {  
    ItemTag findByItem(Item item);  
    ItemTag findByItemId(Long itemId);  
}

@Test  
void queryMethodEntity() throws Exception {  
    //LEFT JOIN  
    Order order1 = new Order("order1");  
    Item item1 = new Item("item1", order1);  
    ItemTag tag1 = new ItemTag("tag1", item1);  
  
    orderRepository.save(order1);  
    itemRepository.save(item1);  
    itemTagRepository.save(tag1);  
  
    em.clear();  
    em.flush();  
  
    ItemTag findItemTag = itemTagRepository.findByItemId(item1.getId());  
    System.out.println(findItemTag.getItem().getItemName());  
}
```

findByItem으로 조회하는 경우는 이미 영속성컨텍스트에 item이 존재하지 때문에 left join 쿼리문이 발생하지 않지만 id로 보내는 경우에는 item의 존재여부를 체크하고 쿼리를 실행시키는 것이 아니기 때문에 left join을 사용해서 fetch join을 진행하게된다. 

![[1.png]]

그런데 연관entity의 id를 단순히 조건으로만 사용하고 fetch join없이 조회해서 최적화를 하고 싶은 경우가 있을 수 있는데 

다음과 같이 query를 직접 작성하는 방식으로 최적화가 가능하다. 

``` java
public interface ItemTagRepository extends JpaRepository<ItemTag, Long> {  
    @Query("select it from ItemTag it where it.item.id = :itemId")  
//    @EntityGraph(attributePaths = {"item"})  
    ItemTag findByItemEntityId(Long itemId);  
}

@Test  
void queryMethodEntity4() throws Exception {  
    //entity graph 있으면 fetch join을 위해 left join 발생
    Order order1 = new Order("order1");  
    Item item1 = new Item("item1", order1);  
    ItemTag tag1 = new ItemTag("tag1", item1);  
  
    orderRepository.save(order1);  
    itemRepository.save(item1);  
    itemTagRepository.save(tag1);  
  
    ItemTag findItemTag = itemTagRepository.findByItemEntityId(item1.getId());  
    Assertions.assertThat(findItemTag).isEqualTo(tag1);  
}
```

이런식으로 @query를 통해 작성한 메서드를 먼저 사용하다가 fetch join이 필요하다가 생각되는 시점에 @EntityGraph를 붙여주는 방식도 괜찮다고 생각한다. 

![[2.png]]
