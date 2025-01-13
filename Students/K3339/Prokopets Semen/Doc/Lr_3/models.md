# Модели

## Описание

Реализовать модели для приложения.

## Стек

- Python
- django
- restFramework
- djoser
- drf_yasg

## Листинг

### models.py:

### Модель BusType содержит:
- Поле имени
- Поле Вместительности

```python
class BusType(models.Model):
    name = models.CharField(max_length=100)
    capacity = models.PositiveIntegerField()

    def __str__(self):
        return f"{self.name} (Capacity: {self.capacity})"
```
### Модель bus содержит: 
- Поле номер автобуса
- Поле тип автобуса
- Поле статус

```python
class Bus(models.Model):
    registration_number = models.CharField(max_length=20, unique=True)
    bus_type = models.ForeignKey(BusType, on_delete=models.CASCADE)
    in_service = models.BooleanField(default=True)

    def __str__(self):
        return f"Bus {self.registration_number}"

```

### Модель Driver содержит:
- Поле паспортные данные водителя
- Поле класс водителя(фикс 3)
- Поле зарплата
- Поле день рождения

```python
class Driver(models.Model):
    passport_data = models.CharField(max_length=100)
    DRIVER_CLASS_CHOICES = [
        ('A', 'Class A'),
        ('B', 'Class B'),
        ('C', 'Class C'),
    ]
    driver_class = models.CharField(max_length=1, choices=DRIVER_CLASS_CHOICES)
    work_experience = models.PositiveIntegerField()
    salary = models.DecimalField(max_digits=10, decimal_places=2)
    date_of_birth = models.DateField(default=date.today)

    def __str__(self):
        return f"Driver {self.passport_data}"


```

### Модель Route содержит:
- Поле номер маршрута
- Поле начальная точка
- Поле конечная точка
- Поле время начала
- Поле время окончания
- Интервал между автобусами
- частота


```python
class Route(models.Model):
    number = models.CharField(max_length=20, unique=True, default='')

    start_point = models.CharField(max_length=100)
    end_point = models.CharField(max_length=100)
    operation_start_time = models.TimeField()
    operation_end_time = models.TimeField()
    interval = models.DurationField(null=True, blank=True)
    duration = models.DurationField(null=True, blank=True)

    def __str__(self):
        return f"Route {self.number}: {self.start_point} - {self.end_point}"
```

### Модель WorkShift содержит:
- Поле driver
- Поле bus
- Поле route
- Поле date
- Поле shift_start_time
- Поле shift_end_time
- Поле reason
- Поле status


```python
class WorkShift(models.Model):
    driver = models.ForeignKey(Driver, on_delete=models.CASCADE)
    bus = models.ForeignKey(Bus, on_delete=models.CASCADE)
    route = models.ForeignKey(Route, on_delete=models.CASCADE)
    date = models.DateField()
    shift_start_time = models.TimeField()
    shift_end_time = models.TimeField()
    STATUS_CHOICES = [
        ('Worked', 'Worked'),
        ('Absent', 'Absent'),
        ('Sick', 'Sick'),
        ('Breakdown', 'Breakdown'),
        ('No Driver', 'No Driver'),
        ('No Bus', 'No Bus'),
    ]
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='Worked')
    reason = models.TextField(null=True, blank=True)

    class Meta:
        unique_together = ('driver', 'date', 'shift_start_time', 'shift_end_time')

    def __str__(self):
        return f"Shift on {self.date}: {self.driver} on {self.bus} for {self.route}"
```
