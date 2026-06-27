# Parte 2: Implementación Práctica en C#

## Desafío 1: Consumo de Endpoints y Deserialización

```csharp
using System;
using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;

public class Alumno
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string.Empty;
    public string Carnet { get; set; } = string.Empty;
}

public class ApiAlumnosService
{
    private static readonly HttpClient client = new HttpClient();

    public async Task<Alumno?> ObtenerAlumnoAsync()
    {
        try
        {
            HttpResponseMessage response = await client.GetAsync("https://api.usac.edu/v1/alumnos");

            response.EnsureSuccessStatusCode();

            string json = await response.Content.ReadAsStringAsync();

            JsonSerializerOptions options = new JsonSerializerOptions
            {
                PropertyNameCaseInsensitive = true
            };

            Alumno? alumno = JsonSerializer.Deserialize<Alumno>(json, options);

            return alumno;
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine("Error en la solicitud HTTP: " + ex.Message);
            return null;
        }
        catch (JsonException ex)
        {
            Console.WriteLine("Error al deserializar el JSON: " + ex.Message);
            return null;
        }
    }
}
```

---

## Desafío 2: Endpoint para Carga Masiva CSV

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using System.IO;
using System.Threading.Tasks;
using System.Collections.Generic;

[ApiController]
[Route("api/[controller]")]
public class AlumnosController : ControllerBase
{
    private readonly AppDbContext _context;

    public AlumnosController(AppDbContext context)
    {
        _context = context;
    }

    [HttpPost("carga-csv")]
    public async Task<IActionResult> CargarCsv(IFormFile archivo)
    {
        if (archivo == null || archivo.Length == 0)
        {
            return BadRequest("Debe seleccionar un archivo CSV válido.");
        }

        List<Alumno> alumnos = new List<Alumno>();

        using (var reader = new StreamReader(archivo.OpenReadStream()))
        {
            string? linea;

            while ((linea = await reader.ReadLineAsync()) != null)
            {
                string[] columnas = linea.Split(',');

                if (columnas.Length >= 3)
                {
                    Alumno alumno = new Alumno
                    {
                        Id = int.Parse(columnas[0]),
                        Nombre = columnas[1],
                        Carnet = columnas[2]
                    };

                    alumnos.Add(alumno);
                }
            }
        }

        await _context.Alumnos.AddRangeAsync(alumnos);

        await _context.SaveChangesAsync();

        return Ok("Carga masiva realizada correctamente.");
    }
}
```

# Parte 3: Referencias Bibliográficas

Facultad de Ingeniería, USAC. (2026). *Sesión 20: Integración de Datos. Consumo de APIs Externas y Carga Masiva (CSV/XML).* Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala.