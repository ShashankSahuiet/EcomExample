# EcomExample
MainClass

***************************************************************************************************************************************
package com.project.EcomExample;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class EcomExampleApplication {

	public static void main(String[] args) {
		SpringApplication.run(EcomExampleApplication.class, args);
	}

}

***************************************************************************************************************************************
Resources --> 


package com.project.EcomExample.Resources;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import com.project.EcomExample.Dtos.ItemRequestDto;
import com.project.EcomExample.Entities.Item;
import com.project.EcomExample.services.ItemService;

@RestController
public class ItemResource {

	
	@Autowired
    private ItemService itemService;

    @GetMapping("/items")
    @ResponseBody
    public List<Item> allItems() {

        return itemService.findAll();
    }
    
    
    
    
    @GetMapping("/items/{id}")
    @ResponseBody
    public Item getItemById(@PathVariable("id") Long id)  {
        return itemService.findById(id);
    }
    
    

    @GetMapping("/items/count")
    @ResponseBody
    public Long count() {

        return itemService.count();
    }

    
    
    @PostMapping(path= "/add/item", consumes = "application/json", produces = "application/json")
    public ItemRequestDto addItem( @RequestBody ItemRequestDto itemRequestDto) {
    	ItemRequestDto result  = itemService.addItem(itemRequestDto);
    	return result;
    }
    
	
	
}









***************************************************************************************************************************************
Service --> 
package com.project.EcomExample.services;

import java.util.List;

import com.project.EcomExample.Dtos.ItemRequestDto;
import com.project.EcomExample.Entities.Item;

public interface ItemService {

	public List<Item> findAll();
	
	public Long count();

	public ItemRequestDto addItem(ItemRequestDto itemRequestDto);



	Item findById(Long id);
}




&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&


package com.project.EcomExample.services;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.project.EcomExample.Database.ItemRepository;
import com.project.EcomExample.Dtos.ItemRequestDto;
import com.project.EcomExample.Entities.Item;

@Component
public class ItemServiceImpl implements ItemService {

	@Autowired
	private ItemRepository itemRepository;

	public List<Item> findAll() {

		Iterable<Item> it = itemRepository.findAll();

		List<Item> users = new ArrayList<Item>();
		it.forEach(e -> users.add(e));

		return users;
	}

	public Long count() {

		return itemRepository.count();
	}

	@Override
	public ItemRequestDto addItem(ItemRequestDto itemRequestDto) {
		Item item  = new Item();
		item.setName(itemRequestDto.getName());
		item.setPrice(itemRequestDto.getPrice());
		
		
		Item res = itemRepository.save(item);
		itemRequestDto.setId(res.getId());
		return itemRequestDto;
	}

	@Override
	public Item findById(Long id) {
 		Optional<Item> op  = itemRepository.findById(id);
 		if(op.isPresent()) {
 			return op.get();
 		}
 		throw new RuntimeException("Item Not found for this Id " + id); 
	}

	
}












***************************************************************************************************************************************
Domain  & Respository -->


package com.project.EcomExample.Database;

import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

import com.project.EcomExample.Entities.Item;

@Repository
public interface ItemRepository extends CrudRepository<Item, Long> {

}

&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&


package com.project.EcomExample.Entities;

import java.io.Serializable;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Item  implements Serializable{
	/**
	 * 
	 */
	private static final long serialVersionUID = -3204530883364307606L;
	@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
	private boolean isSold ;  
	private String name ;
	private Long  price  ;
	
	
	public Item() {
		
	}


	public Long getId() {
		return id;
	}


	public void setId(Long id) {
		this.id = id;
	}


	public boolean isSold() {
		return isSold;
	}


	public void setSold(boolean isSold) {
		this.isSold = isSold;
	}


	public String getName() {
		return name;
	}


	public void setName(String name) {
		this.name = name;
	}


	public Long getPrice() {
		return price;
	}


	public void setPrice(Long price) {
		this.price = price;
	}


	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		result = prime * result + (isSold ? 1231 : 1237);
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		result = prime * result + ((price == null) ? 0 : price.hashCode());
		return result;
	}


	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Item other = (Item) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		if (isSold != other.isSold)
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		if (price == null) {
			if (other.price != null)
				return false;
		} else if (!price.equals(other.price))
			return false;
		return true;
	}


	@Override
	public String toString() {
		return "ItemEntity [id=" + id + ", isSold=" + isSold + ", name=" + name + ", price=" + price + "]";
	}
	
	
	
	

	
	
}







***************************************************************************************************************************************
DTOs --> 


public class ItemRequestDto {

	private Long id; 
	private String name;
	private Long price;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Long getPrice() {
		return price;
	}
	public void setPrice(Long price) {
		this.price = price;
	}
	public Long getId() {
		return id;
	}
	public void setId(Long id) {
		this.id = id;
	}
	
	
	

}




***************************************************************************************************************************************
Application.properties 


spring.datasource.url=jdbc:mysql://localhost:3306/Ecom
spring.datasource.username=root
spring.datasource.password=root


spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
server.servlet.context-path=/ecom






***************************************************************************************************************************************


