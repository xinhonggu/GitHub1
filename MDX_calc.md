GitHub1
=======
/*
The CALCULATE command controls the aggregation of leaf cells in the cube.
If the CALCULATE command is deleted or modified, the data within the cube is affected.
You should edit this command only if you manually specify how the cube is aggregated.
*/
CALCULATE;     
/* Calculations to aggregate Internet Sales and Reseller Sales measures */
CREATE MEMBER CURRENTCUBE.[Measures].[Total Sales Amount]
 AS [Measures].[Internet Sales-Sales Amount]+[Measures].[Reseller Sales-Sales Amount], 
FORMAT_STRING = "Currency", 
NON_EMPTY_BEHAVIOR = { [Internet Sales-Sales Amount], [Reseller Sales-Sales Amount] }, 
VISIBLE = 1;    
CREATE MEMBER CURRENTCUBE.[Measures].[Total Product Cost]
 AS [Measures].[Internet Sales-Total Product Cost]+[Measures].[Reseller Sales-Total Product Cost], 
FORMAT_STRING = "Currency", 
NON_EMPTY_BEHAVIOR = { [Internet Sales-Total Product Cost], [Reseller Sales-Total Product Cost] }, 
VISIBLE = 1;   
/* Calculations to calculate gross profit margin */
CREATE MEMBER CURRENTCUBE.[Measures].[Internet GPM]
 AS ([Measures].[Internet Sales-Sales Amount] - 
[Measures].[Internet Sales-Total Product Cost]) /
[Measures].[Internet Sales-Sales Amount], 
FORMAT_STRING = "Percent", 
NON_EMPTY_BEHAVIOR = { [Internet Sales-Sales Amount] }, 
VISIBLE = 1;   
CREATE MEMBER CURRENTCUBE.[Measures].[Reseller GPM]
 AS ([Measures].[Reseller Sales-Sales Amount] - 
[Measures].[Reseller Sales-Total Product Cost]) /
[Measures].[Reseller Sales-Sales Amount], 
FORMAT_STRING = "Percent", 
NON_EMPTY_BEHAVIOR = { [Reseller Sales-Sales Amount] }, 
VISIBLE = 1;   
CREATE MEMBER CURRENTCUBE.[Measures].[Total GPM]
 AS ([Measures].[Total Sales Amount] - 
[Measures].[Total Product Cost]) /
[Measures].[Total Sales Amount], 
FORMAT_STRING = "Percent", 
NON_EMPTY_BEHAVIOR = { [Internet Sales-Sales Amount], [Reseller Sales-Sales Amount] }, 
VISIBLE = 1;  
/* Calculations to calculate percentage of product to total product sales */
CREATE MEMBER CURRENTCUBE.[Measures].[Internet Sales Ratio to All Products]
 AS Case
    When IsEmpty( [Measures].[Internet Sales-Sales Amount] ) 
    Then 0
    Else ( [Product].[Product Categories].CurrentMember,
           [Measures].[Internet Sales-Sales Amount]) /
         ( [Product].[Product Categories].[(All)].[All], 
           [Measures].[Internet Sales-Sales Amount] )
    End, 
FORMAT_STRING = "Percent", 
NON_EMPTY_BEHAVIOR = { [Internet Sales-Sales Amount] }, 
VISIBLE = 1;  
CREATE MEMBER CURRENTCUBE.[Measures].[Reseller Sales Ratio to All Products]
 AS Case
    When IsEmpty( [Measures].[Reseller Sales-Sales Amount] ) 
    Then 0
    Else ( [Product].[Product Categories].CurrentMember,
           [Measures].[Reseller Sales-Sales Amount]) /
         ( [Product].[Product Categories].[(All)].[All], 
           [Measures].[Reseller Sales-Sales Amount] )
    End, 
FORMAT_STRING = "Percent", 
NON_EMPTY_BEHAVIOR = { [Reseller Sales-Sales Amount] }, 
VISIBLE = 1;  
CREATE MEMBER CURRENTCUBE.[Measures].[Total Sales Ratio to All Products]
 AS Case
    When IsEmpty( [Measures].[Total Sales Amount] ) 
    Then 0
    Else ( [Product].[Product Categories].CurrentMember,
           [Measures].[Total Sales Amount]) /
         ( [Product].[Product Categories].[(All)].[All], 
           [Measures].[Total Sales Amount] )
    End, 
FORMAT_STRING = "Percent", 
NON_EMPTY_BEHAVIOR = { [Internet Sales-Sales Amount], [Reseller Sales-Sales Amount] }, 
VISIBLE = 1; 
/* named sets */
CREATE DYNAMIC SET CURRENTCUBE.[Core Products]
 AS [Product].[Category].&[1] ; 
CREATE DYNAMIC SET CURRENTCUBE.[Large Resellers]
 AS Exists([Reseller].[Reseller Name].[Reseller Name].Members,
[Reseller].[Number of Employees].&[4]) ; 
