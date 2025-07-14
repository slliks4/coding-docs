This is basically calling another function to create a model when a model is created

e.g
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=CustomUser)
def create_profile(sender, instance, created, **kwargs):
    if created:
        custom_user_profile = CustomUserProfile(user=instance)
        custom_user_profile.save()

