# Код программы на языке GPSS

```
		logic r carToLoadAvailable
product1Incoming	generate 10,2
		queue unloadingQueue
		transfer ,product1Unload
 
product2Incoming	generate 20,5
		queue unloadingQueue
		transfer ,product2Unload

; Unloading products by robotUnloader
product1Unload	seize robotUnloader
		depart unloadingQueue
		advance 10,4
		queue product1Storage,500
		release robotUnloader
		transfer ,loadCar

product2Unload	seize robotUnloader
		depart unloadingQueue
		advance 20,6
		queue product2Storage,2000
		release robotUnloader
		
loadCar 		gate u carLoader
		gate ls carToLoadAvailable
		logic r carToLoadAvailable
		test GE q$product1Storage,1000
		test GE q$product2Storage,1000
		depart product1Storage,1000
		depart product2Storage,1000
		queue productsInCars,2000
		terminate

; Loading of transport trucks
carsIncoming	generate 10,5
		queue carsQueue
		test GE q$product1Storage,1000
		test GE q$product2Storage,1000
		seize carLoader
		depart carsQueue
		logic s carToLoadAvailable
		advance 10,5
		release carLoader
		logic r carToLoadAvailable
		terminate

; Modeling time
	generate 	480
	savevalue max_Product1_Amount,qm$product1Storage
	savevalue max_product1_Batches_Count,(x$max_Product1_Amount/500)
	savevalue max_Product2_Amount,qm$product2Storage
	savevalue max_product2_Batches_Count,(x$max_Product2_Amount/2000)
	savevalue productsInCars,q$productsInCars
	terminate 1

; Start simulation
	start 1
	clear

```