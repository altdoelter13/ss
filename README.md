local RS=game:GetService("ReplicatedStorage")
local notparented={}
createdonserver={}
local playerindex={}
local instanceindex={}
local cooldown=2
local blacklist={}
local moved={}
local chats={}
local chatblacklist={}
local newparentt={}
local alredyy={}
local foldermain=Instance.new("Folder",RS) foldermain.Name="FD"
local fdpart=Instance.new("Part",foldermain) fdpart.Anchored=true fdpart.Name="FDPart"
local Storage=Instance.new("Folder",foldermain) Storage.Name="Storage"
local remote=Instance.new("RemoteFunction",foldermain) remote.Name="FD"
local remote2=Instance.new("RemoteEvent",foldermain) remote2.Name="FD2"
local replicatebackparts={}
ez=nil
remote.OnServerInvoke = function(client,Arg,InstanceClass,FullName,Parent,Instancee)
	ez=nil
	if InstanceClass~="TouchTransmitter" then
	if Arg=="EasyCreation" and Instancee==nil and FullName~=nil then
			local a=Instance.new(InstanceClass,fdpart) a.Name=Parent
			if a:IsA("BasePart") then
				a.Position=Vector3.new(3000,5000,3000)
				a.Anchored=true
			end
			local t={client,a}
		table.insert(playerindex,t)
		table.insert(instanceindex,a)
		table.insert(newparentt,{a,FullName})
		
		return a;
	elseif Instancee~=nil then
		return "Fail";
		end
	else
		return "Fail";
	end
end

local fixparts={}
local checked={}
local checked2={}
local temp={}

local function find(tbl,obj)
	for i,v in pairs(tbl) do
		if v==obj then
			return i;
		end
	end
	
	return false;
end

local function checklol(Obj)
	local lol = false
	if find(checked2,Obj) then
		return false;
	end
	for i,v in pairs(temp) do
		if v:IsDescendantOf(workspace) then
		if v:IsA("Weld") or v:IsA("Motor6D") then
			if v.Part1==Obj or v.Part0==Obj then
				lol = true
			end
			end
		end
	end
	for i,v in pairs(Obj:GetDescendants()) do
		if v:IsA("Weld") or v:IsA("Motor6D") then
			if v.Part1==Obj or v.Part0==Obj then
				lol = true
			end
		end
	end
	for i,v in pairs(Obj:GetDescendants()) do
		if lol==true then
			if not find(fixparts,v) and not find(checked2,v) then
				table.insert(fixparts,v)
			    table.insert(checked2,v)
			end
		end
	end
	if Obj:IsA("Weld") or Obj:IsA("Motor6D") then
		if not find(fixparts,Obj) then
			table.insert(fixparts,Obj)
			table.insert(checked2,Obj)
		end
		lol=true
	end
	if Obj:FindFirstAncestorWhichIsA("Model") then
		local a = Obj:FindFirstAncestorWhichIsA("Model")
		if a.ClassName=="Model" then
			for i,v in pairs(a:GetDescendants()) do
				if v:IsA("Weld") or v:IsA("Motor6D") then
					if v.Part1==Obj or v.Part0==Obj then
						lol=true
					end
				end
			end
			for i,v in pairs(a:GetDescendants()) do
				if lol==true then
					if not find(fixparts,v) and not find(checked2,v) then
						table.insert(fixparts,v)
						table.insert(checked2,v)
					end
				end
			end
		end
	end
	if lol==false then
		return false
	else
		return true
	end
end

remote2.OnServerEvent:Connect(function(client,Arg,InstanceClass,FullName,Variable)
	local rs,err = pcall(function()
		if Arg=="Property" then
			for i,v in pairs(InstanceClass) do
				local rs,err = pcall(function()
					local skip=false
					local Var=v[2]
					local Obj=v[1]
					local setto=v[3]
					if not find(checked,Obj) then
						table.insert(checked,Obj)
						checklol(Obj)
					end
					if find(fixparts,Obj) then
						if Var=="Origin Position" or Var=="Origin Orientation" or Var=="World Pivot Position" or Var=="Origin World Pivot Orientation" or Var=="RotVelocity" or Var=="Velocity" or Var=="CFrame" or Var=="Position" or Var=="MaxVelocity" or Var=="CurrentAngle" or Var=="Axis" or Var=="SecondaryAxis" or Var=="WorldSecondaryAxis" or Var=="WorldCFrame" or Var=="C0" or Var=="C1" then --or Var=="Orientation" or Var=="Rotation"
							if string.match(Obj.ClassName,"Part") or Obj:IsA("Model") or Obj:IsA("Weld") or Obj:IsA("Attachment") or Obj:IsA("Motor6D") then
								local rs,err = pcall(function()
									if Obj:IsDescendantOf(workspace) or game:GetService("Players"):FindFirstChild(Obj.Name) and Obj:IsA("Model") and Obj.Parent==workspace then--client.Name~=v.Name and
										if not find(moved,Obj) then
											skip=false
											table.insert(moved,Obj)
										elseif find(moved,Obj) then
											skip=true
										end
									end
								end)
							end
						end
					else
						skip=false
					end
					if Var=="TimePosition" then
						skip=true
					end
					local authorized=true
					for i,v in pairs(playerindex) do
						if v[2]==Obj then
							if client~=v[1] and Var~="Text" and Var~="Health" and Var~="DisplayName" and Var~="Name" and Var~="BrickColor" then
								skip=true
							end
						end
					end
					if Var=="C0" or Var=="C1" or Var=="CFrame" or string.match(Var,"Origin") then
						if find(alredyy,Obj) then
							skip=true
							else
							table.insert(alredyy,Obj)
						end
					end
					if find(instanceindex,Obj) and Var=="Parent" and setto==Storage then
						skip=true
					end
					if skip==false then
						Obj[Var]=setto
					end
				end)
			end
		end
	end)
end)

local testt=0
while wait(0.1) do
	testt=testt+0.1
	for i,v in pairs(newparentt) do
		v[1].Parent=v[2]
		table.remove(newparentt,i)
	end
	if testt>=cooldown then
		alredyy={}
		testt=0
		for i,v in pairs(blacklist) do
			local rs,err = pcall(function()
				if not v:IsDescendantOf(game) then
					table.remove(blacklist,i)
				end
			end)
		end
		moved={}
		remote2:FireAllClients(blacklist)
		chatblacklist={}
	end
end
