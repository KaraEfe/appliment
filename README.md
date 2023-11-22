local player = game.Players.LocalPlayer
local Character = player.Character
local humanoid = Character:WaitForChild("Humanoid")

local statusFolder = Character:WaitForChild("StatusFolder")  
local uis = game:GetService("UserInputService")
local ts = game:GetService("TweenService")

local Tool = player.Backpack.NikoStyle
local equipped = false


Tool.Equipped:Connect(function()
	equipped = true
end)

Tool.Unequipped:Connect(function()
	equipped = false
end)



local params = RaycastParams.new()
params.FilterType = Enum.RaycastFilterType.Whitelist
params.FilterDescendantsInstances = {workspace.Map}

local remote = game.ReplicatedStorage.NikoEvent
local resources = game.ReplicatedStorage.Fx


local UIS = game:GetService("UserInputService")
local remote = game.ReplicatedStorage.NikoEvent
local lastTimeM1 = 0
local lastM1End = 0
local combo = 1


local blocking = false

local canAir = true

local punchAnims = {
	'rbxassetid://13252631188',--1
	'rbxassetid://13252633972',--2
	'rbxassetid://13252636425',--3
	'rbxassetid://13252638414',--4
	'rbxassetid://13252640234',--5

}
local blockAnims = {
	'rbxassetid://11605724915' -- Blocking
}


local blockAnims = {
	'rbxassetid://11605724915' -- Blocking
}


local airAnims = {
	'rbxassetid://11988887051', -- Kick Up
	'rbxassetid://11988635525', -- Kick Down




}


local function hb(size, cframe, ignore, char)
	local hb = Instance.new("Part", workspace.Fx)
	hb.Anchored = true
	hb.CanCollide = false
	hb.Transparency = .6
	hb.Name = "hb"
	hb.Material = Enum.Material.ForceField
	hb.CanQuery = false
	hb.Size = size
	hb.CFrame= cframe

	local con
	con = hb.Touched:Connect(function()
		con:Disconnect()
	end)

	local lasttarg

	for i, v in pairs(hb:GetTouchingParts()) do
		if v.Parent:FindFirstChild("Humanoid") and table.find(ignore,v.Parent) == nil then
			if lasttarg then
				if (lasttarg.Position - char.PrimaryPart.Position).Magnitude > (v.Position -  char.PrimaryPart.Position).Magnitude then
					lasttarg = v.Parent.PrimaryPart
				end
			else
				lasttarg = v.Parent.PrimaryPart
			end
		end
	end
	hb:Destroy()
	if lasttarg then
		return lasttarg.Parent
	else
		return nil
	end
end

local function crater(data)
	local currentTime = tick()
	local craterRaycastResult
	repeat
		craterRaycastResult = workspace:Raycast(data.Target.PrimaryPart.Position, data.Direction, params)
		print(craterRaycastResult)
		wait()
	until tick() - currentTime > 5 or craterRaycastResult ~= nil
	if craterRaycastResult then
		for i = 0, 14 do
			local part = Instance.new("Part", workspace.Fx)
			part.Anchored = true
			part.CFrame = CFrame.new(craterRaycastResult.Position, craterRaycastResult.Position + craterRaycastResult.Normal)
			part.CFrame = part.CFrame * CFrame.Angles(math.rad(90), math.rad(i * 360/14), 0) * CFrame.new(0,0,-4 * 2) * CFrame.Angles(math.rad(35),0,0)
			part.CanQuery = false
			part.CanCollide = false
			part.CanTouch = false

			local result = workspace:Raycast(part.Position + craterRaycastResult.Normal + 4, craterRaycastResult.Normal * -5, params)
			if result then
				part.Position = result.Position
				part.Material = result.Material
				part.Color = result.Instance.Color
			else
				part:Destroy()
			end

			part.Position = part.Position + craterRaycastResult.Normal * -4
			ts:Create(part, TweenInfo.new(.2, Enum.EasingStyle.Cubic, Enum.EasingDirection.Out, 0, false, 0),{Position = part.Positon + craterRaycastResult.Normal * -4}):Play()

			spawn(function()
				game.Debris:AddItem(part, 4)
				ts:Create(part, TweenInfo.new(.5, Enum.EasingStyle.Cubic, Enum.EasingDirection.Out, 0, false, 0),{Position = part.Positon + craterRaycastResult.Normal * -4}):Play()
			end)

			if i % 3 < 2 and result then
				local rubble = part:Clone()
				rubble.Size = Vector3.new(math.random(10,20)/20,math.random(10,20)/20,math.random(10,20)/20)
				rubble.Position = result.Position + craterRaycastResult.Normal * 4
				rubble.Material = result.Material
				rubble.Color = result.Instance.Color	

				rubble.Parent = workspace.Fx
				rubble.Anchored = false
				rubble.CanCollide= true	

				local bv = Instance.new("BodyVelocity", rubble)	
				bv.Velocity = Vector3.new(math.random(-40,40), 30, math.fandom(-40, 40))
				bv.MaxForce = Vector3.new(99999,99999,99999)
				bv.Name = "Velocity"	

				game.Debris:AddItem(bv,.1)	
				game.Debris:AddItem(rubble,4)

				spawn(function()
					wait(2)

					ts:Create(rubble, TweenInfo.new(1, Enum.EasingStyle.Cubic, Enum.EasingDirection.Out, 0, false, 0), {Transparency = 1}):Play()
				end)

				rubble.Transparency = 0	
			end
		end
	end
end

local function finisher(data)
	local cf = data.Character.PrimaryPart.CFrame
	for i = 1, 20 do
		local rock = Instance.new("Part")
		rock.Size = Vector3.new(1,1,1)
		rock.CFrame = cf * CFrame.new(3,0,-4 + (i * -1))
		rock.CanCollide = false
		rock.Anchored = true
		local result = workspace:Raycast(rock.Position, Vector3.new(0,-4,0), params)
		if result then
			rock.Position = result.Position
			rock.Material = result.Material
			rock.Color = result.Instance.Color
			rock.Rotation = Vector3.new(math.random(0,180),math.random(0,180),math.random(0,180))
			rock.Parent = workspace.Fx
			game.Debris:AddItem(rock, 2)
		else
			rock:Destroy()
		end	


		local rock = Instance.new("Part")
		rock.Size = Vector3.new(1,1,1)
		rock.CFrame = cf * CFrame.new(-3,0,-4 + (i * -1))
		rock.CanCollide = false
		rock.Anchored = true
		local result = workspace:Raycast(rock.Position, Vector3.new(0,-4,0), params)
		if result then
			rock.Position = result.Position
			rock.Material = result.Material
			rock.Color = result.Instance.Color

			rock.Rotation = Vector3.new(math.random(0,180),math.random(0,180),math.random(0,180))
			rock.Parent = workspace.Fx
			game.Debris:AddItem(rock, 2)
		else
			rock:Destroy()
		end

		if i % 4 == 0 then
			wait()
		end
		if i == 20 then
			for n = 1, 4 do
				local rock = Instance.new("Part")
				rock.Size = Vector3.new(1,1,1)
				rock.CFrame = cf  * CFrame.new(3 - (n/1.5),0,-25 + (n * -.5))
				rock.CanCollide = false
				rock.Anchored = true
				local result = workspace:Raycast(rock.Position, Vector3.new(0,-4,0), params)
				if result then
					rock.Position = result.Position
					rock.Material = result.Material
					rock.Color = result.Instance.Color

					rock.Rotation = Vector3.new(math.random(0,180),math.random(0,180),math.random(0,180))
					rock.Parent = workspace.Fx
					game.Debris:AddItem(rock, 2)
				else
					rock:Destroy()
				end

				local rock = Instance.new("Part")
				rock.Size = Vector3.new(1,1,1)
				rock.CFrame = cf * CFrame.new(-3 + (n/2),0,25 + (n * -.5))
				rock.CanCollide = false
				rock.Anchored = true
				local result = workspace:Raycast(rock.Position, Vector3.new(0,-4,0), params)
				if result then
					rock.Position = result.Position
					rock.Material = result.Material
					rock.Color = result.Instance.Color

					rock.Rotation = Vector3.new(math.random(0,180),math.random(0,180),math.random(0,180))
					rock.Parent = workspace.Fx
					game.Debris:AddItem(rock, 2)
				else
					rock:Destroy()
				end

			end
		end
	end
end

uis.InputBegan:Connect(function(input, gameProcessedEvent)
	if gameProcessedEvent then return end
	if not equipped then return end
	if Character:FindFirstChild("Stun") then return end
	if input.UserInputType == Enum.UserInputType.MouseButton1 and tick() - lastTimeM1 > .3 and tick() - lastM1End > .7 and not blocking and statusFolder:FindFirstChild("Stun") == nil then
		if tick() - lastTimeM1 > .7 then
			combo = 1
		end

		lastTimeM1 = tick()

		local animation = Instance.new("Animation", workspace.Fx)
		local air = nil

		if uis:IsKeyDown("Space") and combo == 4 and canAir then
			canAir = false
			animation.AnimationId = airAnims[1]
		elseif not uis:IsKeyDown("Space") and combo == 5 and not canAir then
			animation.AnimationId = airAnims[2]
			air = "Down"
		else
			animation.AnimationId = punchAnims[combo]
		end

		local load = humanoid:LoadAnimation(animation)
		load:Play()

		animation:Destroy()

		local hitTarg = hb(Vector3.new(4,6,4),  Character.PrimaryPart.CFrame * CFrame.new(0,0,-3),  {Character}, Character)

		if hitTarg then
			local data = {
				["Target"] = hitTarg,
				["Character"] = Character,
				["Combo"] = combo,
				["Air"] = air,
				["Action"] = "m1",
			}

			remote:FireServer(data)
		end
		local Sound = Instance.new("Sound")
		Sound.Parent = Character:FindFirstChild("HumanoidRootPart")
		Sound.SoundId = "rbxassetid://5835032207"
		Sound:Play()
		Sound.Volume = 0.2
		if combo == #punchAnims then
			combo = 1
			lastM1End = tick()
		else
			combo += 1
		end
		humanoid.WalkSpeed = 0
		wait(.4)
		humanoid.WalkSpeed = 16
	elseif input.KeyCode == Enum.KeyCode.F and tick() - lastTimeM1 > .3 and statusFolder:FindFirstChild("Stun") == nil then
		blocking = true
		local animation = Instance.new("Animation", workspace.Fx)
		animation.AnimationId = blockAnims[1] 
		local blockAnimation = humanoid.Animator:LoadAnimation(animation)
		blockAnimation:Play()
		animation:Destroy()

		local data = {
			["Action"] = "Block",
			["Character"] = Character
		}

		remote:FireServer(data)
		repeat
			wait()
		until blocking == false or statusFolder:FindFirstChild("Stun")
		blocking = false
		blockAnimation:Stop()
	end
end)

uis.InputEnded:Connect(function(input, gpe)
	if input.KeyCode == Enum.KeyCode.F and blocking then
		blocking = not blocking
		local data = {
			["Action"] = "Unblock",
			["Character"] = Character
		}

		remote:FireServer(data)
	end
end)

humanoid.StateChanged:Connect(function(old, new)
	if new == Enum.HumanoidStateType.Landed then
		canAir = true
	end
end)

local function hitEffect(target)
	for i,particle in pairs(resources.Hit3:GetChildren()) do
		local clone = particle:Clone()
		clone.Parent = target.UpperTorso.BodyFrontAttachment
		clone:Emit(clone:GetAttribute("EmitCount"))
		game.Debris:AddItem(clone, .3)
	end
end


local function blockEffect(target)
	for i,particle in pairs(resources.Hit3:GetChildren()) do
		local clone = resources.Ring:Clone()
		clone.Parent = target.UpperTorso.BodyFrontAttachment
		clone:Emit(clone:GetAttribute("EmitCount"))
		game.Debris:AddItem(clone, .3)
	end
end
remote.OnClientEvent:Connect(function(data)
	if data.Action == "Crater" then
		crater(data)
	elseif data.Action == "Finisher" then
		finisher(data)
	elseif data.Action == "Hit" then
		hitEffect(data.Target)
	elseif data.Action == "Block" then
		blockEffect(data.Target)
	end
end)

statusFolder.ChildAdded:Connect(function(Child)
	local WalkSpeed = 16
	local JumpHeight = 7.2
	for i,status in pairs(statusFolder:GetChildren()) do
		if status.Name == "Stun" then
			WalkSpeed = 0 
			JumpHeight = 0
		elseif status.Name == "Block" then
			WalkSpeed = 3
			JumpHeight = 0
		end
	end
	humanoid.WalkSpeed = WalkSpeed
	humanoid.JumpHeight = JumpHeight
end)

statusFolder.ChildRemoved:Connect(function(Child)
	local WalkSpeed = 16
	local JumpHeight = 7.2
	if blocking then
		WalkSpeed = 0 
		JumpHeight = 0
	end
	for i,status in pairs(statusFolder:GetChildren()) do
		if status.Name == "Stun" then
			WalkSpeed = 0 
			JumpHeight = 0
		elseif status.Name == "Block" then
			WalkSpeed = 3
			JumpHeight = 0
		end
	end
	humanoid.WalkSpeed = WalkSpeed
	humanoid.JumpHeight = JumpHeight
end)

uis.JumpRequest:Connect(function()
	if tick() - lastTimeM1 < 1 then
		humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
	else
		humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
	end
end)
