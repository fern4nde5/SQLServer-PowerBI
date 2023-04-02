<p align="center"><img src="https://user-images.githubusercontent.com/120461225/229381026-516543c2-2dc6-47b3-a3f9-3bc66e76f092.png"></p>
<h1 align="center">PROJETO DE INTEGRAÇÃO SQL Server - Power BI</h1>
<p align="center">Projeto de Integração SQL Server com Power BI, proposto como desafio no curso SQL Impressionador - Hashtag Treinamentos, para desenvolver os conceitos e práticas aprendidos durante o curso.</p>



<p align="center"><img src="https://user-images.githubusercontent.com/120461225/229381133-f3e88783-a89f-439b-a962-c4e2ee7d30d1.png"></p>


<p>A partir do banco de dados disponibilizados pela Microsoft, o <strong>Adventure Works 2014</strong>, utilizando o <strong>Microsoft SQL Server Management Studio</strong>, e o Power BI para exibir as informações analisadas em um <em>Dashboard</em>, com <em>gráfico de barra</em>, <em>gráfico de linha</em>, <em>gráfico de rosca</em> e <em>mapa</em>. Esse projeto foi feito durante um curso como atividade pr&aacute;tica, proposto como desafio final do módulo de <em>SQL Server</em>.</p>

## Informações Exibidas
<ul>
 <li>Receita Total x Custo Total por País.</li>
 <li>Vendas por Categoria.</li>
 <li>Vendas por Gênero.</li>
 <li>Vendas por Mês.</li>
</ul>

## KPIs do Projeto
<ul>
 <li>Receita Total.</li>
 <li>Quantidade Vendida.</li>
 <li>Receita Total e Lucro Total por Mês.</li>
 <li>Clientes por País e Gênero.</li>
</ul>

## Passo a passo
Primeiro foi feito o download do banco de dados, disponibilizado pela Microsoft, após isso, foi aberto o banco de dados para observar os dados disponíveis e planejar como utilizar esses dados.
![1](https://user-images.githubusercontent.com/120461225/229361830-3b508438-4595-43e8-ba29-a555c5df3a6d.jpg)

Após observar os dados disponíveis, foram selecionadas as tabelas que serão úteis para encontrar o resultado esperado. As tabelas selecionados foram <em>FactInternetSales, DimProductCategory, DimProduct, DimGeography e DimCustomer.</em>

```
SELECT * FROM DimProduct
SELECT * FROM FactInternetSales
SELECT * FROM DimGeography
SELECT * FROM DimCustomer
SELECT * FROM DimProductCategory
```

A seguir, foram selecionadas as linhas desejadas e renomeadas para uma melhor visualização:

```
SELECT
	SalesOrderNumber AS 'N° Pedido',
	OrderDate AS 'Data', 
	EnglishProductCategoryName AS 'Categoria',
	CustomerKey AS 'ID Cliente',
	FirstName + ' ' + LastName AS 'Nome',
	REPLACE(REPLACE(Gender, 'M', 'Masculino'), 'F', 'Feminino') AS 'Gênero',
	EnglishCountryRegionName 'País',
	OrderQuantity 'Qtde Vendida',
	SalesAmount AS 'Total Venda',
	TotalProductCost 'Total Custo',
	SalesAmount - TotalProductCost AS 'Lucro'
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
		INNER JOIN DimGeography DG
			ON DC.GeographyKey = DG.GeographyKey
```

O código até aqui, ficou da seguinte maneira, aplicando as alterações de nome de tabelas e JOIN's:

```
SELECT
	FIS.SalesOrderNumber AS 'N° Pedido',
	FIS.OrderDate AS 'Data', 
	DPC.EnglishProductCategoryName AS 'Categoria',
	FIS.CustomerKey AS 'ID Cliente',
	DC.FirstName + ' ' + DC.LastName AS 'Nome',
	REPLACE(REPLACE(DC.Gender, 'M', 'Masculino'), 'F', 'Feminino') AS 'Gênero',
	DG.EnglishCountryRegionName 'País',
	FIS.OrderQuantity 'Qtde Vendida',
	FIS.SalesAmount AS 'Total Venda',
	FIS.TotalProductCost 'Total Custo',
	FIS.SalesAmount - FIS.TotalProductCost AS 'Lucro'
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
		INNER JOIN DimGeography DG
			ON DC.GeographyKey = DG.GeographyKey
```

As tabelas foram renomeadas por suas siglas.

O primeiro INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimProduct e FactInternetSales, através da coluna ProductKey.
O segundo INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimProductSubcategory e DimProduct, através da coluna ProductSubcategoryKey.
O terceiro INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimProductCategory e DimProductSubcategory, através da coluna ProductCategoryKey.
Esses JOINs foram primordiais para relacionar a Categorias das Vendas com as Vendas.

O quarto INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimCustomer e FactInternetSales, através da coluna CustomerKey.
O quinto INNER JOIN foi feito para unir os entrelaçamentos entre as tabelas DimGeography e FactInternetSales, através da coluna CustomerKey.
Esses JOINs foram primordiais para relacionar o País das Vendas com os Clientes.

No final, foi usado o replace para renomear, na coluna gênero, F, por Feminino e M por Masculino.

```
REPLACE(REPLACE(DC.Gender, 'M', 'Masculino'), 'F', 'Feminino') AS 'Gênero',
```

Com o código pronto, foi criado a seguinte visualização RESULTADO_ADW:


```
CREATE OR ALTER VIEW RESULTADO_ADW AS
SELECT
	FIS.SalesOrderNumber AS 'N° Pedido',
	FIS.OrderDate AS 'Data', 
	DPC.EnglishProductCategoryName AS 'Categoria',
	FIS.CustomerKey AS 'ID Cliente',
	DC.FirstName + ' ' + DC.LastName AS 'Nome',
	REPLACE(REPLACE(DC.Gender, 'M', 'Masculino'), 'F', 'Feminino') AS 'Gênero',
	DG.EnglishCountryRegionName 'País',
	FIS.OrderQuantity 'Qtde Vendida',
	FIS.SalesAmount AS 'Total Venda',
	FIS.TotalProductCost 'Total Custo',
	FIS.SalesAmount - FIS.TotalProductCost AS 'Lucro'
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
		INNER JOIN DimGeography DG
			ON DC.GeographyKey = DG.GeographyKey
```

O resultado dessa view podemos ver abaixo:
![2](https://user-images.githubusercontent.com/120461225/229381622-a85833fb-139a-4f8a-a3b1-99c21db91f9f.jpg)


# Integração com o Power BI
A partir de agora, será feita a integração com o Power BI.

## Obtendo Dados
Para obter os dados do SQL Server, foi selecionado o servidor e a view criada RESULTADO_ADW:
![3](https://user-images.githubusercontent.com/120461225/229381784-c30928dc-c869-4ce6-90b5-9e24897f5c1c.jpg)
![4](https://user-images.githubusercontent.com/120461225/229381787-30eed865-9a48-457b-a386-950162618e67.jpg)
![5](https://user-images.githubusercontent.com/120461225/229381835-a6beb024-559d-43b0-b18c-4339c249ffc1.jpg)

## Criação do DashBoard
Com os dados obtidos, agora é criar os gráficos e modificar o visual para melhorar a visualização.
Assim ficou o Dashboard, dividido em 3 páginas:

Capa:
![6](https://user-images.githubusercontent.com/120461225/229381993-10580ee4-b671-4532-8a30-a38dacbb8f39.jpg)

Geral:
![7](https://user-images.githubusercontent.com/120461225/229381998-a6870ba7-bce0-47d6-b5ac-96de8478c72b.jpg)

Clientes:
![8](https://user-images.githubusercontent.com/120461225/229382010-b9e9bcb9-6f79-4e3e-a298-46c981daec27.jpg)


## Resultado Final
Com as tabelas dinâmicas criadas, agora é só utilizar essas para criar os gráficos para o dashboard e definir a aparência do dashboard.
Assim ficou o resultado final:

![23](https://user-images.githubusercontent.com/120461225/229364924-ea8bbb07-4429-4e6d-bb70-756421a7b068.jpg)

## Conclusão
Com essa integração, os dados podem ser atualizados a qualquer momento no Power BI, mantendo a visualização do Dashboard para exibição futura.
</body>

