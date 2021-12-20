## Код программы

```

; Products incoming to warehouse
		logic r carToLoadAvailable
product1Incoming	generate 10,2 			; Product1 batch incoming
		queue unloadingQueue		; Put product 1 batch into unloadingQueue
		transfer,product1Unload 	; Go to product1 unloading
 
product2Incoming	generate 20,5			; Product2 batch incoming
		queue unloadingQueue		; Put product 2 batch into unloadingQueue
		transfer,product2Unload		; Go to product2 unloading

; Unloading products by robotUnloader
product1Unload	seize robotUnloader		; Seize robotUnloader facility
		depart unloadingQueue		; Take product1 batch from a single unloadingQueue
		advance 10,4			; Unload product 1 batch
		queue product1Storage,500	; Put goods to storage
		release robotUnloader
		transfer,loadCar

product2Unload	seize robotUnloader
		depart unloadingQueue
		advance 20,6
		queue product2Storage,2000
		release robotUnloader
		
loadCar 		gate u carLoader
		gate ls carToLoadAvailable
		logic r carToLoadAvailable
		test GE q$product1Storage,1000		; Check if there are 1000 entries of product1
		test GE q$product2Storage,1000		; Check if there are 1000 entries of product2
		depart product1Storage,1000			; Load 1000 items of product1
		depart product2Storage,1000			; Load 1000 items of product2
		queue productsInCars,2000
		terminate

; Loading of transport trucks
carsIncoming	generate 10,5					; Cars incoming
		queue carsQueue					; Put a car into a queue
		test GE q$product1Storage,1000		; Check if there are 1000 entries of product1
		test GE q$product2Storage,1000		; Check if there are 1000 entries of product2
		seize carLoader					; Seize carLoader (facility that loads cars)
		depart carsQueue					; Take a car from loading queue
		logic s carToLoadAvailable
		advance 10,5					; Car loading for 10+-5 minutes
		release carLoader
		logic r carToLoadAvailable
		terminate					; Complete transaction path

; Modeling time
	generate 	480	; modeling 8 hours (480 minutes) of work
	savevalue max_Product1_Amount,qm$product1Storage	; Save max amount of products of type 1 stored in warehouse
	savevalue max_Product2_Amount,qm$product2Storage	; Save max amount of products of type 2 stored in warehouse
	savevalue max_Products_Amount,(x$max_Product1_Amount+x$max_Product2_Amount) ; Save max amount of products of both types in warhouse
	savevalue productsInCars,q$productsInCars
	terminate 1

; Start simulation
	start 1
	clear
		
```

