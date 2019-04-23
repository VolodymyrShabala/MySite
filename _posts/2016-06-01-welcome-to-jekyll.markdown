---
layout: post
title:  "Polarity Shift"
color:  blue
width:   3
height:  1
date:   2019-03-01 11:31:49 +0200
categories: jekyll update
---
During 4 weeks of game development using Unreal and C++ I and my group developed coop puzzle 2.5D game.

During the project I worked on camera, menu, tutorial etc. But my main focus was on code refactoring and bug fixing. During week two when all the important mechanics were implemented I started refactoring the code for easier bug fixing, to easier read code and easier time implementing future features.

Code samples comming soon. But you can download and play the game in the link down below.

<iframe width="840" height="630" src="{{site.url}}/Portfolio/assets/PolarityShift_Trailer.mp4" frameborder="0" allowfullscreen="allowfullscreen">&nbsp;</iframe>

<a href="https://drive.google.com/open?id=1xekIfMAPPeOHBK5hPr1w9AH1wMI8EZK9">Download game here</a>

Code sample: 
After I refactored
{% highlight C# %}
void USMMovableComponent::TickComponent(float DeltaTime, enum ELevelTick TickType, FActorComponentTickFunction *ThisTickFunction){
	Super::TickComponent(DeltaTime, TickType, ThisTickFunction);
	if(!AttachedToActor){
		return;
	}

	FVector MoveDirection = AttachedToActor->GetActorLocation() - GetOwner()->GetActorLocation();
	FVector MoveAmount = MoveDirection * FieldMoveSpeed * FApp::GetDeltaTime();

	if (MoveAmount.Size() > ReleaseThreshold) {
		Detach();
		return;
	}

	MoveAmount.X = 0;
	GetOwner()->AddActorWorldOffset(MoveAmount, true);

	if (!CarriedCharacter)
		return;

	if(CarriedCharacter->bRequirePickupConsent){
		if(bCarriedPositive){
			if(!CarriedCharacter->MagnetComponent->bIsPositive){
				Detach();
				return;
			}
		} else{
			if(!CarriedCharacter->MagnetComponent->bIsNegative){
				Detach();
				return;
			}
		}
	}
}
{% endhighlight %}

Before:
{% highlight C++ %}
void USMMovableComponent::TickComponent(float DeltaTime, enum ELevelTick TickType, FActorComponentTickFunction *ThisTickFunction) {
	Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

	FHitResult Hit;
	if (AttachedToActor) {
		if (CarriedCharacter) {	
			if (CarriedCharacter->bRequirePickupConsent) {
				if (bCarriedPositive) {
					if (CarriedCharacter->MagnetComponent->bIsPositive) {
					} else {
						Detach();
						return;
					}

				} else {
					if (CarriedCharacter->MagnetComponent->bIsNegative) {
					} else {
						Detach();
						return;
					}
				}
			}
		}

		FVector CurrentLocation = GetOwner()->GetActorLocation();
		FVector TargetLocation = AttachedToActor->GetActorLocation();

		FVector NewLocation = TargetLocation - CurrentLocation;

		if (NewLocation.Size() > ReleaseThreshold) {
			Detach();
			return;
		}

		FVector MoveAmount = NewLocation * FieldMoveSpeed * FApp::GetDeltaTime();

		GetOwner()->AddActorLocalOffset(MoveAmount, true, &Hit);
	} else {
		Detach();
	}
}
{% endhighlight %}