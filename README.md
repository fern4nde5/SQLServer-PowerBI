<p align="center"><img src="https://user-images.githubusercontent.com/120461225/229360953-62cf4894-7d5f-4146-835b-c9f13509a74f.png"></p>
<h1 align="center">PROJETO DE INTEGRAÇÃO SQL Server - Excel</h1>
<p align="center">Projeto de Integração SQL Server com Excel, proposto como desafio no curso SQL Impressionador - Hashtag Treinamentos, para desenvolver os conceitos e práticas aprendidos durante o curso.</p>


<p align="center"><img src="https://user-images.githubusercontent.com/120461225/229361161-3cdd77a2-0207-4c1c-8776-013d9f64a8e6.jpg"></p>

<p>A partir do banco de dados disponibilizados pela Microsoft, o <strong>Adventure Works 2014</strong>, utilizando o <strong>Microsoft SQL Server Management Studio</strong>, e o Excel para exibir as informações analisadas em um <em>Dashboard</em>, com <em>gráfico de barra</em>, <em>gráfico de pizza</em>, <em>gráfico de rosca</em>, e <em>gráfico de barras</em>. Esse projeto foi feito durante um curso como atividade pr&aacute;tica, proposto como desafio final do módulo de <em>SQL Server</em>.</p>

## Informações Exibidas
<ul>
 <li>Receita Total x Custo Total por País.</li>
 <li>Vendas por Categoria.</li>
 <li>Vendas por Gênero.</li>
 <li>Vendas por Mês.</li>
</ul>

## KPIs do Projeto
<ul>
 <li>Total de Vendas pela Internet por Categoria.</li>
 <li>Receita Total pela Internet por Categoria.</li>
 <li>Receita e Custo Total pela Internet por País.</li>
 <li>Total de Vendas pela Internet por Gênero.</li>
 
 Com a observação de que o ano de análise será somente para os pedidos de 2013. 
</ul>

## Passo a passo
Primeiro foi feito o download do banco de dados, disponibilizado pela Microsoft, após isso, foi aberto o banco de dados para observar os dados disponíveis e planejar como utilizar esses dados.
![1](https://user-images.githubusercontent.com/120461225/229361830-3b508438-4595-43e8-ba29-a555c5df3a6d.jpg)

Após observar os dados disponíveis, foram selecionadas as tabelas que serão úteis para encontrar o resultado esperado. As tabelas selecionados foram <em>FactInternetSales, DimProductCategory, DimProduct, DimProductSubcategory, DimSalesTerritory e DimCustomer.</em>

```
SELECT * FROM FactInternetSales
SELECT * FROM DimProductCategory
SELECT * FROM DimProduct
SELECT * FROM DimProductSubcategory
SELECT * FROM DimSalesTerritory
SELECT * FROM DimCustomer
```

A seguir, foram selecionadas as linhas desejadas e renomeadas para uma melhor visualização:

```
SELECT
	SalesOrderNumber AS 'N° Pedido',
	OrderDate AS 'Data',
	EnglishProductCategoryName AS 'Categoria',
	FirstName + ' ' + LastName AS 'Cliente',
	SalesTerritoryCountry AS 'País',
	OrderQuantity AS 'Qtde. Vendida',
	TotalProductCost AS 'Custo',
	SalesAmount AS 'Receita',
	Gender AS 'Gênero'
FROM
	FactInternetSales
```

Em seguida, foi aplicado a cláusula JOIN, para unir as Chaves Primárias e Chaves Estrangeiras, além disso foi dado nomes as tabelas para facilitar a visualização:


```
INNER JOIN DimProduct DP
	ON DP.ProductKey = FIS.ProductKey
		INNER JOIN DimProductSubcategory DPS
			ON DPS.ProductSubcategoryKey = DP.ProductSubcategoryKey
				INNER JOIN DimProductCategory DPC
					ON DPC.ProductCategoryKey = DPS.ProductCategoryKey
INNER JOIN DimCustomer DC
	ON DC.CustomerKey = FIS.CustomerKey
INNER JOIN DimSalesTerritory DST 
	ON DST.SalesTerritoryKey = FIS.SalesTerritoryKey
```

O código até aqui, ficou da seguinte maneira, aplicando as alterações de nome de tabelas e JOIN's:

```
SELECT
	FIS.SalesOrderNumber AS 'N° Pedido',
	FIS.OrderDate AS 'Data',
	EnglishProductCategoryName AS 'Categoria',
	DC.FirstName + ' ' + DC.LastName AS 'Cliente',
	SalesTerritoryCountry AS 'País',
	FIS.OrderQuantity AS 'Qtde. Vendida',
	FIS.TotalProductCost AS 'Custo',
	FIS.SalesAmount AS 'Receita',
	Gender AS 'Gênero'
FROM
	FactInternetSales FIS
INNER JOIN DimProduct DP
	ON DP.ProductKey = FIS.ProductKey
		INNER JOIN DimProductSubcategory DPS
			ON DPS.ProductSubcategoryKey = DP.ProductSubcategoryKey
				INNER JOIN DimProductCategory DPC
					ON DPC.ProductCategoryKey = DPS.ProductCategoryKey
INNER JOIN DimCustomer DC
	ON DC.CustomerKey = FIS.CustomerKey
INNER JOIN DimSalesTerritory DST 
	ON DST.SalesTerritoryKey = FIS.SalesTerritoryKey
```

As tabelas foram renomeadas por suas siglas.

O primeiro INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimProduct e FactInternetSales, através da coluna ProductKey.
O segundo INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimProductSubcategory e DimProduct, através da coluna ProductSubcategoryKey.
O terceiro INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimProductCategory e DimProductSubcategory, através da coluna ProductCategoryKey.
Esses JOINs foram primordiais para relacionar a Categorias das Vendas com as Vendas.

O quarto INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimCustomer e FactInternetSales, através da coluna CustomerKey.
O quinto INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimSalesTerritory e FactInternetSales, através da coluna SalesTerritoryKey.
Esses JOINs foram primordiais para relacionar o País das Vendas com as Vendas.

No final, para selecionar apenas os pedidos feitos no ano de 2013, o WHERE foi utilizado para filtrar.

```
WHERE YEAR(FIS.OrderDate) = 2013
```

Com o código pronto, foi criado a seguinte visualização VENDAS_INTERNET:


```
CREATE OR ALTER VIEW VENDAS_INTERNET AS

SELECT
	FIS.SalesOrderNumber AS 'N° Pedido',
	FIS.OrderDate AS 'Data',
	EnglishProductCategoryName AS 'Categoria',
	DC.FirstName + ' ' + DC.LastName AS 'Cliente',
	SalesTerritoryCountry AS 'País',
	FIS.OrderQuantity AS 'Qtde. Vendida',
	FIS.TotalProductCost AS 'Custo',
	FIS.SalesAmount AS 'Receita',
	Gender AS 'Gênero'
FROM
	FactInternetSales FIS
INNER JOIN DimProduct DP
	ON DP.ProductKey = FIS.ProductKey
		INNER JOIN DimProductSubcategory DPS
			ON DPS.ProductSubcategoryKey = DP.ProductSubcategoryKey
				INNER JOIN DimProductCategory DPC
					ON DPC.ProductCategoryKey = DPS.ProductCategoryKey
INNER JOIN DimCustomer DC
	ON DC.CustomerKey = FIS.CustomerKey
INNER JOIN DimSalesTerritory DST 
	ON DST.SalesTerritoryKey = FIS.SalesTerritoryKey
WHERE YEAR(FIS.OrderDate) = 2013
```

O resultado dessa view podemos ver abaixo:
![24](https://user-images.githubusercontent.com/120461225/229363616-9e3aa97a-ed47-47eb-b2db-70bc62e17417.jpg)


# Integração com o Excel
A partir de agora, será feita a integração com o Excel.

## Obtendo Dados
Para obter os dados do SQL Server, foi selecionado o servidor e a view criada VENDAS_INTERNET:
![2](https://user-images.githubusercontent.com/120461225/229364438-6d403cb3-aee3-4f29-833f-df80dfb3c69b.jpg)
![3](https://user-images.githubusercontent.com/120461225/229364463-17e8ff20-c6c7-4cd1-8f6b-7c635e4d44eb.jpg)
![4](https://user-images.githubusercontent.com/120461225/229364483-16e833d7-1545-405f-9390-086c87452eb1.jpg)
![8](https://user-images.githubusercontent.com/120461225/229364519-aa2cd576-9698-48e3-ba35-912bfe36d06b.jpg)


## Modificando Dados
Com os dados obtidos, alguns dados precisam ser modificados para melhorar a visualização.
Primeiro, na tabela gênero, foi substituído M por Masculino e F por Feminino.
![10](https://user-images.githubusercontent.com/120461225/229364604-f9a14ff5-d73b-499c-b7a9-826b9e49c09c.jpg)

Após isso, foi criado uma nova coluna para exibir o mês do ano da venda, e depois selecionado apenas as 3 primeiras letras para abreviar:
![11](https://user-images.githubusercontent.com/120461225/229364649-6e6773d7-ba40-4cf7-9bdd-a7bbfe499f6d.jpg)
![12](https://user-images.githubusercontent.com/120461225/229364651-fc24526d-d76f-43dd-b4eb-7867541caaa3.jpg)
![14](https://user-images.githubusercontent.com/120461225/229364690-623cd2d7-8708-4ef7-9ed0-ae872fa586a8.jpg)

No final, a tabela ficou assim:
![15](https://user-images.githubusercontent.com/120461225/229364710-2fe8b629-1247-457d-ab63-eefcbbe794b3.jpg)

## Tabelas Dinâmicas
A partir da tabela obtida, foi criado tabelas dinâmicas para obter os dados desejados:
Quantidade Vendida x Categoria
![16](https://user-images.githubusercontent.com/120461225/229364775-05897394-bc18-4915-a0ad-c69bcf41baa8.jpg)

Quantidade Vendida x Mês
![18](https://user-images.githubusercontent.com/120461225/229364793-e30786f8-51aa-47b8-9cb3-04c8a01cfe40.jpg)

Quantidade Vendida x Gênero
![20](https://user-images.githubusercontent.com/120461225/229364811-1a7ada3c-edc6-40d2-840e-2f9734894fa7.jpg)

No final, assim ficou as tabelas dinâmicas criadas:
![17](https://user-images.githubusercontent.com/120461225/229364841-161cb844-61f1-43c8-9d8e-633683539eed.jpg)
![19](https://user-images.githubusercontent.com/120461225/229364848-11c8a4ca-ff13-4d42-8e54-3c0101f47b1a.jpg)
![22](https://user-images.githubusercontent.com/120461225/229364852-1afcb211-bdd0-4989-bad1-46bbc09b0102.jpg)



## Resultado Final
Com as tabelas dinâmicas criadas, agora é só utilizar essas para criar os gráficos para o dashboard e definir a aparência do dashboard.
Assim ficou o resultado final:

![23](https://user-images.githubusercontent.com/120461225/229364924-ea8bbb07-4429-4e6d-bb70-756421a7b068.jpg)

## Conclusão
Com essa integração, os dados podem ser atualizados a qualquer momento no Excel, mantendo a visualização do Dashboard para exibição futura.
</body>

