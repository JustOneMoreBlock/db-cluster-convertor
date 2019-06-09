Example to add a virtual primary key.
```
ALTER TABLE `your_database`.`mod_invoicedata`   
  ADD COLUMN `virtual_pk` INT(11) NOT NULL AFTER `customfields`, 
  ADD PRIMARY KEY (`virtual_pk`);
```