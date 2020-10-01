# DRF channels WS

Proekt AzerbaycanPythonCommunity ucun publish olunur

## Installation

* `pip3 install drf-async-ws`

## Usage

### Nece isdeyir(example)
```python
from django.db import models
from rest_framework import serializers
from drf_async.jsonasync import AzPUG

class Garage(models.Model):
    name = models.CharField(max_length=255)

class Car(models.Model):
    name = models.CharField(max_length=255)
    garage = models.ForeignKey('Garage', on_delete=models.CASCADE)


class CarSerializer(serializers.ModelSerializer):
    class Meta:
        model = Bird
        fields = '__all__'


class CarWS(AzPUG):
    model = Car
    stream = 'cars'
    serializer = CarSerializer

    @classmethod
    def group_names(cls, instance):
        return [instance.garage.name]

class GarageWS(AzPUG):
    """
    serializers-siz versiyasi 
    """
    model = Garage
    stream = 'garage-cars'

    @classmethod
    def group_names(cls, instance):
        return [instance.name]

    def serialize_data(self, instance):
        return {'id': instance.id, 'name': instance.name}
```
Bundan sonra emin olunki WebsocketConsumer-de duzgun add eleyirsiz grupu misal   `self.channel_layer.group_add('bmw', self.channel_name)`  
`Garage.objects.create(name='bmw')` bundan sonra duzgun websocket connect olacag. Serializersiz versiya ucun.!

```json
{
    "stream": "garage-cars",
    "payload": {
        "action": "create",
        "data": {"id": 1, "name": "bmw"},
        "model": "your_app.garage",
        "pk": 1
    }
}
```

Hemcinin (`"action": "update"`) ve ya (`"action": "delete"`) eyni netice alacagsiz.