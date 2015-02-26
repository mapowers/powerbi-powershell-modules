# PowerBIPS.psm1

A powershell module with cmdlets to interact with the PowerBI developer APIs.

PowerBI developer API documentation: https://msdn.microsoft.com/library/dn877544

## Get PowerBI Authentication Token

```powershell

$authToken = Get-PBIAuthToken -clientId "4c3c58d6-8c83-48c2-a604-67c1b740d167"

$authTokenWithUsername = Get-PBIAuthToken -clientId "4c3c58d6-8c83-48c2-a604-67c1b740d167" -userName "<username>" -password "<password>"

```

## List DataSets

```powershell

$dataSets = Get-PBIDataSets -authToken $authToken

```

## Test the existence of a DataSet

```powershell

if (Test-PBIDataSet -authToken $authToken -dataSetName "TestDataSet")
{
	Write-Host "true"
}
else
{
	Write-Host "false"
}

```

## Create a DataSet

```powershell

$dataSetSchema = @{
	name = "TestDataSet"	
    ; tables = @(
		@{ 	name = "TestTable"
			; columns = @( 
				@{ name = "Id"; dataType = "Int64"  }
				, @{ name = "Name"; dataType = "String"  }
				, @{ name = "Date"; dataType = "DateTime"  }
				, @{ name = "Value"; dataType = "Double"  }
				) 
		})
}	

$createdDataSet = New-PBIDataSet -authToken $authToken -dataSet $dataSetSchema -Verbose

```

## Add Rows to a table

```powershell

$sampleRows = 1..53 |% {	
	@{
		Id = $_
		; Name = "Record $_"
		; Date = [datetime]::Now
		; Value = (Get-Random -Minimum 10 -Maximum 1000)
	}
}

# Push the rows into PowerBI in batches of 10 records

$sampleRows | Add-PBITableRows -authToken $authToken -dataSetName "TestDataSet" -tableName "TestTable" -batchSize 10 -Verbose

```

## Delete rows of a table

```powershell

Clear-PBITableRows -authToken $authToken -dataSetName "TestDataSet" -tableName "TestTable" -Verbose

```

## Full Script Sample

```powershell

cls

Import-Module PowerBIPS -Force

$authToken = Get-PBIAuthToken -clientId "4c3c58d6-8c83-48c2-a604-67c1b740d167"

if (-not (Test-PBIDataSet -authToken $authToken -dataSetName "TestDataSet"))
{
	$dataSetSchema = @{
		name = "TestDataSet"	
	    ; tables = @(
			@{ 	name = "TestTable"
				; columns = @( 
					@{ name = "Id"; dataType = "Int64"  }
					, @{ name = "Name"; dataType = "String"  }
					, @{ name = "Date"; dataType = "DateTime"  }
					, @{ name = "Value"; dataType = "Double"  }
					) 
			})
	}	
	
	$createdDataSet = New-PBIDataSet -authToken $authToken -dataSet $dataSetSchema -Verbose
}
else
{
	Clear-PBITableRows -authToken $authToken -dataSetName "TestDataSet" -tableName "TestTable" -Verbose
}

$sampleRows = 1..53 |% {	
	@{
		Id = $_
		; Name = "Record $_"
		; Date = [datetime]::Now
		; Value = (Get-Random -Minimum 10 -Maximum 1000)
	}
}

$sampleRows | Add-PBITableRows -authToken $authToken -dataSetName "TestDataSet" -tableName "TestTable" -batchSize 10 -Verbose

```
