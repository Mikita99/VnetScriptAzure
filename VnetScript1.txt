# Define variables
$resourceGroupName = "test1"
$location = "eastus"  # e.g., "East US"


# Select Azure subscription
# If you have multiple subscriptions, you may need to adjust this command to select the correct one
Set-AzContext -SubscriptionId "YourSubscriptionId"

# Prompt user for the number of virtual networks to create
$numVirtualNetworks = Read-Host "Enter the number of virtual networks to create"

# Validate input
if (-not [int]::TryParse($numVirtualNetworks, [ref]$null) -or [int]$numVirtualNetworks -le 0) {
    Write-Error "Invalid input. Please enter a positive integer for the number of virtual networks."
    exit
}

# Create virtual networks
for ($i = 1; $i -le $numVirtualNetworks; $i++) {
    $vnetName = Read-Host "Enter the name for Virtual Network $i"
    Write-Host "Creating virtual network $vnetName..."
    
    $vnet = New-AzVirtualNetwork `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -Name $vnetName `
        -AddressPrefix "10.$i.0.0/16"  # Adjust address prefix as needed

    # Example: Create subnets within the virtual network
    $subnet1 = New-AzVirtualNetworkSubnetConfig `
        -Name "Subnet1" `
        -AddressPrefix "10.$i.1.0/24"

    $subnet2 = New-AzVirtualNetworkSubnetConfig `
        -Name "Subnet2" `
        -AddressPrefix "10.$i.2.0/24"

    $vnet | Add-AzVirtualNetworkSubnetConfig -Name $subnet1 -AddressPrefix $subnet1.AddressPrefix
    $vnet | Add-AzVirtualNetworkSubnetConfig -Name $subnet2 -AddressPrefix $subnet2.AddressPrefix

    # Apply changes to the virtual network
    $vnet | Set-AzVirtualNetwork

   
}
