import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

const SEMESTRES = 8;

const materiasPorSemestre = [
  [
    { nombre: "Matemática I" },
    { nombre: "Física I" },
    { nombre: "Química" },
    { nombre: "Biología" },
  ],
  [
    { nombre: "Matemática II" },
    { nombre: "Física II" },
    { nombre: "Bioquímica" },
    { nombre: "Anatomía" },
  ],
  [], [], [], [], [], [] // Completás según tu malla
];

const estados = ["pendiente", "en curso", "aprobado"];

export default function MallaCurricular() {
  const [estadoMaterias, setEstadoMaterias] = useState(() => {
    const saved = localStorage.getItem("estadoMaterias");
    return saved ? JSON.parse(saved) : {};
  });

  useEffect(() => {
    localStorage.setItem("estadoMaterias", JSON.stringify(estadoMaterias));
  }, [estadoMaterias]);

  const cambiarEstado = (sem, idx) => {
    const key = `${sem}-${idx}`;
    const actual = estadoMaterias[key] || 0;
    const siguiente = (actual + 1) % estados.length;
    setEstadoMaterias({ ...estadoMaterias, [key]: siguiente });
  };

  return (
    <div className="p-4 grid grid-cols-1 md:grid-cols-2 gap-4">
      {materiasPorSemestre.map((materias, sem) => (
        <div key={sem} className="border rounded-2xl shadow p-4">
          <h2 className="text-xl font-bold mb-2">Semestre {sem + 1}</h2>
          <div className="grid grid-cols-1 gap-2">
            {materias.map((mat, idx) => {
              const estadoIdx = estadoMaterias[`${sem}-${idx}`] || 0;
              const estado = estados[estadoIdx];
              const color = {
                pendiente: "bg-gray-200",
                "en curso": "bg-yellow-200",
                aprobado: "bg-green-300",
              }[estado];

              return (
                <Card
                  key={idx}
                  className={`${color} cursor-pointer`} 
                  onClick={() => cambiarEstado(sem, idx)}
                >
                  <CardContent className="p-4">
                    <div className="font-semibold">{mat.nombre}</div>
                    <div className="text-sm italic">{estado}</div>
                  </CardContent>
                </Card>
              );
            })}
          </div>
        </div>
      ))}
    </div>
  );
}
