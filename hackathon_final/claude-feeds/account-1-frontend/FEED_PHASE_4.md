# Frontend Phase 4: Polish + Extras (Hours 9-12)
## Branch: `feature/fe-phase-4-polish-extras`

---

## AI/ML IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  NO AI - PURE UI + EXPORT FEATURES                     │
│  ─────────────────────────────────                     │
│                                                         │
│  Route Optimizer UI:                                    │
│  → POST /api/routes/compare (Pareto algorithm)         │
│  → Not AI - pure algorithmic optimization              │
│                                                         │
│  Cargo Tracking UI:                                     │
│  → Mock/live tracking data                             │
│  → Not AI - just data visualization                    │
│                                                         │
│  Analytics Dashboard:                                   │
│  → Charts with Recharts library                        │
│  → PDF/Excel export with jspdf + xlsx                  │
│                                                         │
│  This phase is ALL UI polish - no AI calls needed!     │
└─────────────────────────────────────────────────────────┘
```

---

## Prerequisites
- Phase FE-3 must be merged to develop
- Pull latest: `git checkout develop && git pull`

---

## Your Mission

Complete remaining pages (Route, Cargo, Analytics), add PDF/Excel export, error boundaries, and empty states.

---

## IMPORTANT: Install Map Dependencies First

```bash
cd frontend
npm install leaflet react-leaflet @types/leaflet
```

Then add this to `src/app/globals.css`:
```css
/* Leaflet Map Styles */
@import 'leaflet/dist/leaflet.css';

.leaflet-container {
  height: 100%;
  width: 100%;
  background: var(--bg-panel);
}
```

---

## Deliverables

### 0. Map Components (REQUIRED - Do This First)

#### ShippingMap.tsx - For Cargo Tracking
```tsx
// src/components/features/cargo/ShippingMap.tsx
'use client'
import { useEffect, useState } from 'react'
import dynamic from 'next/dynamic'

// Dynamic import to avoid SSR issues with Leaflet
const MapContainer = dynamic(
  () => import('react-leaflet').then((mod) => mod.MapContainer),
  { ssr: false }
)
const TileLayer = dynamic(
  () => import('react-leaflet').then((mod) => mod.TileLayer),
  { ssr: false }
)
const Marker = dynamic(
  () => import('react-leaflet').then((mod) => mod.Marker),
  { ssr: false }
)
const Popup = dynamic(
  () => import('react-leaflet').then((mod) => mod.Popup),
  { ssr: false }
)
const Polyline = dynamic(
  () => import('react-leaflet').then((mod) => mod.Polyline),
  { ssr: false }
)

interface Position {
  latitude: number
  longitude: number
  location_name: string
}

interface ShippingMapProps {
  currentPosition: Position
  route?: { lat: number; lon: number; name: string }[]
  originPort?: { lat: number; lon: number; name: string }
  destinationPort?: { lat: number; lon: number; name: string }
}

export function ShippingMap({ currentPosition, route, originPort, destinationPort }: ShippingMapProps) {
  const [isMounted, setIsMounted] = useState(false)

  useEffect(() => {
    setIsMounted(true)
  }, [])

  if (!isMounted) {
    return (
      <div className="h-full w-full bg-panel flex items-center justify-center">
        <div className="font-pixel text-sm animate-pulse">LOADING_MAP...</div>
      </div>
    )
  }

  const center: [number, number] = [currentPosition.latitude, currentPosition.longitude]

  // Create route path
  const routePath: [number, number][] = route?.map(p => [p.lat, p.lon]) || []

  return (
    <MapContainer
      center={center}
      zoom={3}
      style={{ height: '100%', width: '100%' }}
      scrollWheelZoom={true}
    >
      {/* Dark/Grayscale Map Tiles */}
      <TileLayer
        attribution='&copy; <a href="https://carto.com/">CARTO</a>'
        url="https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png"
      />

      {/* Route Line */}
      {routePath.length > 0 && (
        <Polyline
          positions={routePath}
          pathOptions={{ color: '#E8E8E8', weight: 2, dashArray: '10, 10' }}
        />
      )}

      {/* Origin Port */}
      {originPort && (
        <Marker position={[originPort.lat, originPort.lon]}>
          <Popup>
            <div className="font-pixel text-xs">
              <div>ORIGIN</div>
              <div>{originPort.name}</div>
            </div>
          </Popup>
        </Marker>
      )}

      {/* Destination Port */}
      {destinationPort && (
        <Marker position={[destinationPort.lat, destinationPort.lon]}>
          <Popup>
            <div className="font-pixel text-xs">
              <div>DESTINATION</div>
              <div>{destinationPort.name}</div>
            </div>
          </Popup>
        </Marker>
      )}

      {/* Current Position (Vessel) */}
      <Marker position={center}>
        <Popup>
          <div className="font-pixel text-xs">
            <div>VESSEL_POSITION</div>
            <div>{currentPosition.location_name}</div>
            <div className="text-[10px] opacity-60">
              {currentPosition.latitude.toFixed(4)}, {currentPosition.longitude.toFixed(4)}
            </div>
          </div>
        </Popup>
      </Marker>
    </MapContainer>
  )
}
```

#### RouteMap.tsx - For Route Optimizer
```tsx
// src/components/features/route/RouteMap.tsx
'use client'
import { useEffect, useState } from 'react'
import dynamic from 'next/dynamic'

const MapContainer = dynamic(
  () => import('react-leaflet').then((mod) => mod.MapContainer),
  { ssr: false }
)
const TileLayer = dynamic(
  () => import('react-leaflet').then((mod) => mod.TileLayer),
  { ssr: false }
)
const Marker = dynamic(
  () => import('react-leaflet').then((mod) => mod.Marker),
  { ssr: false }
)
const Popup = dynamic(
  () => import('react-leaflet').then((mod) => mod.Popup),
  { ssr: false }
)
const Polyline = dynamic(
  () => import('react-leaflet').then((mod) => mod.Polyline),
  { ssr: false }
)
const CircleMarker = dynamic(
  () => import('react-leaflet').then((mod) => mod.CircleMarker),
  { ssr: false }
)

interface Port {
  code: string
  name: string
  lat: number
  lon: number
  congestion?: 'LOW' | 'MEDIUM' | 'HIGH'
}

interface Route {
  id: string
  name: string
  waypoints: { lat: number; lon: number; name: string }[]
  color: string
  recommended?: boolean
}

interface RouteMapProps {
  origin: Port
  destination: Port
  routes: Route[]
  selectedRouteId?: string
}

// Major ports data
const MAJOR_PORTS: Port[] = [
  { code: 'CNSZX', name: 'Yantian, Shenzhen', lat: 22.5431, lon: 114.0579, congestion: 'MEDIUM' },
  { code: 'CNSHA', name: 'Shanghai', lat: 31.2304, lon: 121.4737, congestion: 'HIGH' },
  { code: 'HKHKG', name: 'Hong Kong', lat: 22.3193, lon: 114.1694, congestion: 'MEDIUM' },
  { code: 'USLAX', name: 'Los Angeles', lat: 33.7701, lon: -118.1937, congestion: 'HIGH' },
  { code: 'USLGB', name: 'Long Beach', lat: 33.7544, lon: -118.2166, congestion: 'MEDIUM' },
  { code: 'NLRTM', name: 'Rotterdam', lat: 51.9244, lon: 4.4777, congestion: 'LOW' },
  { code: 'SGSIN', name: 'Singapore', lat: 1.2644, lon: 103.8222, congestion: 'LOW' },
]

export function RouteMap({ origin, destination, routes, selectedRouteId }: RouteMapProps) {
  const [isMounted, setIsMounted] = useState(false)

  useEffect(() => {
    setIsMounted(true)
  }, [])

  if (!isMounted) {
    return (
      <div className="h-full w-full bg-panel flex items-center justify-center">
        <div className="font-pixel text-sm animate-pulse">LOADING_MAP...</div>
      </div>
    )
  }

  // Center between origin and destination
  const centerLat = (origin.lat + destination.lat) / 2
  const centerLon = (origin.lon + destination.lon) / 2

  const getCongestionColor = (congestion?: string) => {
    switch (congestion) {
      case 'HIGH': return '#FF4141'
      case 'MEDIUM': return '#FFB800'
      case 'LOW': return '#00C853'
      default: return '#888888'
    }
  }

  return (
    <MapContainer
      center={[centerLat, centerLon]}
      zoom={2}
      style={{ height: '100%', width: '100%' }}
      scrollWheelZoom={true}
    >
      <TileLayer
        attribution='&copy; <a href="https://carto.com/">CARTO</a>'
        url="https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png"
      />

      {/* Draw all routes */}
      {routes.map((route) => (
        <Polyline
          key={route.id}
          positions={route.waypoints.map(w => [w.lat, w.lon] as [number, number])}
          pathOptions={{
            color: selectedRouteId === route.id ? '#FFFFFF' : route.color,
            weight: selectedRouteId === route.id ? 4 : 2,
            opacity: selectedRouteId === route.id ? 1 : 0.5,
            dashArray: route.recommended ? undefined : '5, 10',
          }}
        />
      ))}

      {/* Major ports */}
      {MAJOR_PORTS.map((port) => (
        <CircleMarker
          key={port.code}
          center={[port.lat, port.lon]}
          radius={8}
          pathOptions={{
            color: getCongestionColor(port.congestion),
            fillColor: getCongestionColor(port.congestion),
            fillOpacity: 0.8,
          }}
        >
          <Popup>
            <div className="font-pixel text-xs">
              <div>{port.name}</div>
              <div className="text-[10px]">
                Congestion: <span style={{ color: getCongestionColor(port.congestion) }}>
                  {port.congestion}
                </span>
              </div>
            </div>
          </Popup>
        </CircleMarker>
      ))}

      {/* Origin marker */}
      <Marker position={[origin.lat, origin.lon]}>
        <Popup>
          <div className="font-pixel text-xs">
            <div className="text-green-500">ORIGIN</div>
            <div>{origin.name}</div>
          </div>
        </Popup>
      </Marker>

      {/* Destination marker */}
      <Marker position={[destination.lat, destination.lon]}>
        <Popup>
          <div className="font-pixel text-xs">
            <div className="text-blue-500">DESTINATION</div>
            <div>{destination.name}</div>
          </div>
        </Popup>
      </Marker>
    </MapContainer>
  )
}
```

#### Custom Marker Icons (Optional but Nice)
```tsx
// src/components/features/map/MapIcons.ts
import L from 'leaflet'

// Vessel icon
export const vesselIcon = new L.Icon({
  iconUrl: '/icons/vessel.png',  // Add this image to public/icons/
  iconSize: [32, 32],
  iconAnchor: [16, 16],
  popupAnchor: [0, -16],
})

// Port icon
export const portIcon = new L.Icon({
  iconUrl: '/icons/port.png',
  iconSize: [24, 24],
  iconAnchor: [12, 12],
  popupAnchor: [0, -12],
})

// Fallback: Use default Leaflet icons (no custom images needed)
export const defaultIcon = new L.Icon.Default()
```

---

### 1. Route Optimizer Page (`/route`) - WITH MAP

```tsx
// src/app/(dashboard)/route/page.tsx
'use client'
import { useState } from 'react'
import { AppLayout } from '@/components/layout/AppLayout'
import { WorkspaceHeader } from '@/components/layout/WorkspaceHeader'
import { RouteCard } from '@/components/features/route/RouteCard'
import { RouteMap } from '@/components/features/route/RouteMap'
import { Button } from '@/components/ui/Button'

const mockRoutes = [
  {
    id: '1',
    name: 'Direct Sea Freight',
    carrier: 'COSCO Shipping',
    transitDays: 18,
    cost: 1850,
    emissions: 245,
    congestionRisk: 'HIGH',
    recommended: false,
    waypoints: [
      { lat: 22.5431, lon: 114.0579, name: 'Yantian' },
      { lat: 33.7701, lon: -118.1937, name: 'Los Angeles' },
    ],
    color: '#FF4141',
  },
  {
    id: '2',
    name: 'Via Long Beach',
    carrier: 'Evergreen Marine',
    transitDays: 19,
    cost: 1720,
    emissions: 238,
    congestionRisk: 'MEDIUM',
    recommended: true,
    savings: 130,
    waypoints: [
      { lat: 22.5431, lon: 114.0579, name: 'Yantian' },
      { lat: 21.0, lon: -157.0, name: 'Hawaii' },
      { lat: 33.7544, lon: -118.2166, name: 'Long Beach' },
    ],
    color: '#00C853',
  },
  {
    id: '3',
    name: 'Express Air Freight',
    carrier: 'FedEx',
    transitDays: 4,
    cost: 8450,
    emissions: 1890,
    congestionRisk: 'LOW',
    recommended: false,
    waypoints: [
      { lat: 22.3193, lon: 114.1694, name: 'Hong Kong HKG' },
      { lat: 33.9425, lon: -118.4081, name: 'Los Angeles LAX' },
    ],
    color: '#FFB800',
  },
]

const origin = { code: 'CNSZX', name: 'Yantian, Shenzhen', lat: 22.5431, lon: 114.0579 }
const destination = { code: 'USLAX', name: 'Los Angeles', lat: 33.7701, lon: -118.1937 }

export default function RoutePage() {
  const [selectedRoute, setSelectedRoute] = useState<string | null>('2')

  return (
    <AppLayout
      rightPanel={
        <div className="flex flex-col h-full">
          <div className="flex justify-between items-center pb-4 border-b border-[#333]">
            <div className="label text-text-inv">ROUTE_MAP</div>
          </div>
          {/* MAP CONTAINER */}
          <div className="flex-1 mt-4 border border-[#333]" style={{ minHeight: '300px' }}>
            <RouteMap
              origin={origin}
              destination={destination}
              routes={mockRoutes}
              selectedRouteId={selectedRoute || undefined}
            />
          </div>
          <div className="mt-4 text-xs text-[#888]">
            <div className="flex items-center gap-2 mb-1">
              <span className="w-3 h-3 rounded-full bg-[#FF4141]"></span>
              <span>High Congestion</span>
            </div>
            <div className="flex items-center gap-2 mb-1">
              <span className="w-3 h-3 rounded-full bg-[#FFB800]"></span>
              <span>Medium Congestion</span>
            </div>
            <div className="flex items-center gap-2">
              <span className="w-3 h-3 rounded-full bg-[#00C853]"></span>
              <span>Low Congestion</span>
            </div>
          </div>
        </div>
      }
    >
      <WorkspaceHeader
        title="ROUTE"
        pixelTitle="OPTIMIZER"
        metaLabel="ANALYSIS ID"
        metaValue="RT_8824_XQ"
      />
      <div className="flex-1 overflow-y-auto p-6">
        <div className="mb-4 p-4 border border-dark bg-canvas">
          <div className="grid grid-cols-2 gap-4">
            <div>
              <div className="label">ORIGIN</div>
              <div className="font-pixel">{origin.name}</div>
            </div>
            <div>
              <div className="label">DESTINATION</div>
              <div className="font-pixel">{destination.name}</div>
            </div>
          </div>
        </div>
        <div className="grid gap-4">
          {mockRoutes.map((route) => (
            <RouteCard
              key={route.id}
              route={route}
              selected={selectedRoute === route.id}
              onClick={() => setSelectedRoute(route.id)}
            />
          ))}
        </div>
      </div>
      <div className="p-6 bg-canvas border-t-2 border-dark flex justify-end gap-4">
        <Button variant="secondary">COMPARE_ALL</Button>
        <Button disabled={!selectedRoute}>SELECT_ROUTE ›</Button>
      </div>
    </AppLayout>
  )
}
```

#### RouteCard.tsx
```tsx
interface RouteCardProps {
  route: {
    id: string
    name: string
    carrier: string
    transitDays: number
    cost: number
    emissions: number
    congestionRisk: string
    recommended: boolean
    savings?: number
  }
  selected: boolean
  onClick: () => void
}

export function RouteCard({ route, selected, onClick }: RouteCardProps) {
  return (
    <div
      onClick={onClick}
      className={`border-2 p-4 cursor-pointer transition-all ${
        selected ? 'border-dark bg-dark/5' : 'border-[#888] hover:border-dark'
      }`}
    >
      <div className="flex justify-between items-start mb-3">
        <div>
          <div className="font-pixel text-lg">{route.name}</div>
          <div className="text-sm opacity-60">{route.carrier}</div>
        </div>
        {route.recommended && (
          <span className="bg-dark text-canvas px-2 py-1 text-xs font-pixel">RECOMMENDED</span>
        )}
      </div>

      <div className="grid grid-cols-4 gap-4 text-sm">
        <div>
          <div className="label">TRANSIT</div>
          <div className="font-pixel">{route.transitDays} DAYS</div>
        </div>
        <div>
          <div className="label">COST</div>
          <div className="font-pixel">${route.cost.toLocaleString()}</div>
        </div>
        <div>
          <div className="label">CO2</div>
          <div className="font-pixel">{route.emissions} KG</div>
        </div>
        <div>
          <div className="label">CONGESTION</div>
          <div className={`font-pixel ${
            route.congestionRisk === 'HIGH' ? 'text-warning' :
            route.congestionRisk === 'MEDIUM' ? 'text-yellow-500' : ''
          }`}>
            {route.congestionRisk}
          </div>
        </div>
      </div>

      {route.savings && (
        <div className="mt-3 pt-3 border-t border-[#888] text-sm text-green-600">
          Save ${route.savings} compared to direct route
        </div>
      )}
    </div>
  )
}
```

### 2. Cargo Tracking Page (`/cargo`) - WITH LIVE MAP

```tsx
// src/app/(dashboard)/cargo/page.tsx
'use client'
import { useState, useEffect } from 'react'
import { AppLayout } from '@/components/layout/AppLayout'
import { WorkspaceHeader } from '@/components/layout/WorkspaceHeader'
import { ShippingMap } from '@/components/features/cargo/ShippingMap'
import { TrackingTimeline } from '@/components/features/cargo/TrackingTimeline'
import { Input } from '@/components/ui/Input'
import { Button } from '@/components/ui/Button'

const mockShipment = {
  container_id: 'TCLU1234567',
  vessel: 'CSCL Saturn',
  carrier: 'COSCO Shipping',
  origin: { lat: 22.5431, lon: 114.0579, name: 'Yantian, Shenzhen' },
  destination: { lat: 33.7544, lon: -118.2166, name: 'Long Beach, CA' },
  route: [
    { lat: 22.5431, lon: 114.0579, name: 'Yantian Port' },
    { lat: 21.0, lon: 130.0, name: 'Pacific Ocean' },
    { lat: 25.0, lon: 180.0, name: 'International Date Line' },
    { lat: 30.0, lon: -150.0, name: 'Mid-Pacific' },
    { lat: 33.7544, lon: -118.2166, name: 'Long Beach' },
  ],
  currentPosition: {
    latitude: 28.5,
    longitude: -155.0,
    location_name: 'Pacific Ocean, approaching Hawaii',
    speed_knots: 18.5,
    heading: 75,
  },
  progress_percent: 65,
  eta: '2026-02-28T14:00:00Z',
}

const mockTimeline = [
  { status: 'CONTAINER_LOADED', location: 'Yantian Port', timestamp: '2026-02-09T14:30:00Z', completed: true },
  { status: 'DEPARTED_ORIGIN', location: 'Yantian Port', timestamp: '2026-02-10T08:00:00Z', completed: true },
  { status: 'IN_TRANSIT', location: 'Pacific Ocean', timestamp: '2026-02-18T12:00:00Z', completed: true, current: true },
  { status: 'ARRIVAL_AT_DESTINATION', location: 'Long Beach Port', timestamp: '2026-02-28T14:00:00Z', completed: false },
  { status: 'CUSTOMS_CLEARANCE', location: 'Long Beach Port', timestamp: '2026-02-29T10:00:00Z', completed: false },
  { status: 'DELIVERED', location: 'Los Angeles Warehouse', timestamp: '2026-03-02T16:00:00Z', completed: false },
]

export default function CargoPage() {
  const [containerId, setContainerId] = useState('TCLU1234567')
  const [shipment, setShipment] = useState(mockShipment)
  const [isTracking, setIsTracking] = useState(true)

  // Simulate real-time position updates
  useEffect(() => {
    if (!isTracking) return

    const interval = setInterval(() => {
      setShipment(prev => ({
        ...prev,
        currentPosition: {
          ...prev.currentPosition,
          // Slowly move towards destination
          longitude: prev.currentPosition.longitude + 0.1,
          latitude: prev.currentPosition.latitude + 0.02,
        },
        progress_percent: Math.min(100, prev.progress_percent + 0.1),
      }))
    }, 5000) // Update every 5 seconds

    return () => clearInterval(interval)
  }, [isTracking])

  const handleTrack = () => {
    // In real app, fetch from API
    setIsTracking(true)
  }

  return (
    <AppLayout
      rightPanel={
        <div className="flex flex-col h-full">
          <div className="flex justify-between items-center pb-4 border-b border-[#333]">
            <div className="label text-text-inv">SHIPMENT_TIMELINE</div>
          </div>
          <div className="flex-1 overflow-y-auto mt-4">
            <TrackingTimeline events={mockTimeline} />
          </div>
          <div className="mt-4 pt-4 border-t border-[#333]">
            <div className="text-xs text-[#888] mb-2">ALERTS</div>
            <div className="text-xs p-2 bg-[#1a1a1a] border border-[#333]">
              <span className="text-yellow-500">WARNING:</span> Port congestion expected at destination
            </div>
          </div>
        </div>
      }
    >
      <WorkspaceHeader
        title="CARGO"
        pixelTitle="TRACKER"
        metaLabel="CONTAINER"
        metaValue={shipment.container_id}
      />

      {/* Search Bar */}
      <div className="p-4 bg-canvas border-b border-dark flex gap-4">
        <Input
          value={containerId}
          onChange={(e) => setContainerId(e.target.value)}
          placeholder="Enter Container ID, B/L, or Vessel..."
          className="flex-1"
        />
        <Button onClick={handleTrack}>TRACK ›</Button>
      </div>

      {/* Main Content - Split View */}
      <div className="flex-1 flex flex-col overflow-hidden">
        {/* Map Section */}
        <div className="flex-1 relative" style={{ minHeight: '350px' }}>
          <ShippingMap
            currentPosition={shipment.currentPosition}
            route={shipment.route}
            originPort={shipment.origin}
            destinationPort={shipment.destination}
          />
          {/* Overlay Info */}
          <div className="absolute top-4 left-4 bg-dark/90 text-text-inv p-4 text-xs border border-[#333]">
            <div className="font-pixel text-lg mb-2">{shipment.vessel}</div>
            <div className="grid grid-cols-2 gap-x-4 gap-y-1">
              <span className="opacity-60">Speed:</span>
              <span>{shipment.currentPosition.speed_knots} knots</span>
              <span className="opacity-60">Heading:</span>
              <span>{shipment.currentPosition.heading}°</span>
              <span className="opacity-60">Position:</span>
              <span>{shipment.currentPosition.location_name}</span>
            </div>
          </div>
          {/* Progress Bar */}
          <div className="absolute bottom-0 left-0 right-0 bg-dark/90 p-4">
            <div className="flex justify-between text-xs text-text-inv mb-2">
              <span>{shipment.origin.name}</span>
              <span className="font-pixel">{shipment.progress_percent.toFixed(0)}%</span>
              <span>{shipment.destination.name}</span>
            </div>
            <div className="h-2 bg-[#333]">
              <div
                className="h-full bg-text-inv transition-all duration-1000"
                style={{ width: `${shipment.progress_percent}%` }}
              />
            </div>
            <div className="text-center text-xs mt-2 text-[#888]">
              ETA: {new Date(shipment.eta).toLocaleDateString()} {new Date(shipment.eta).toLocaleTimeString()}
            </div>
          </div>
        </div>

        {/* Shipment Details */}
        <div className="p-4 bg-panel border-t border-dark">
          <div className="grid grid-cols-4 gap-4 text-sm">
            <div>
              <div className="label">CARRIER</div>
              <div className="font-pixel">{shipment.carrier}</div>
            </div>
            <div>
              <div className="label">VESSEL</div>
              <div className="font-pixel">{shipment.vessel}</div>
            </div>
            <div>
              <div className="label">CONTAINER</div>
              <div className="font-pixel">{shipment.container_id}</div>
            </div>
            <div>
              <div className="label">STATUS</div>
              <div className="font-pixel text-green-600">IN_TRANSIT</div>
            </div>
          </div>
        </div>
      </div>
    </AppLayout>
  )
}
```

#### TrackingTimeline.tsx
```tsx
interface TimelineEvent {
  status: string
  location: string
  timestamp: string
  completed: boolean
  current?: boolean
}

export function TrackingTimeline({ events }: { events: TimelineEvent[] }) {
  return (
    <div className="relative">
      {events.map((event, i) => (
        <div key={i} className="flex gap-4 pb-6 last:pb-0">
          {/* Timeline line */}
          <div className="flex flex-col items-center">
            <div className={`w-4 h-4 border-2 ${
              event.completed ? 'bg-dark border-dark' :
              event.current ? 'border-dark animate-pulse' : 'border-[#888]'
            }`} />
            {i < events.length - 1 && (
              <div className={`w-0.5 flex-1 ${event.completed ? 'bg-dark' : 'bg-[#888]'}`} />
            )}
          </div>

          {/* Content */}
          <div className="flex-1 pb-4">
            <div className="font-pixel text-sm">{event.status.replace(/_/g, ' ')}</div>
            <div className="text-xs opacity-60">{event.location}</div>
            <div className="text-xs opacity-40 mt-1">
              {new Date(event.timestamp).toLocaleString()}
            </div>
          </div>
        </div>
      ))}
    </div>
  )
}
```

### 3. Analytics Page with Export (`/analytics`)

#### SavingsChart.tsx
```tsx
// src/components/features/analytics/SavingsChart.tsx
'use client'
import { AreaChart, Area, XAxis, YAxis, Tooltip, ResponsiveContainer, BarChart, Bar, PieChart, Pie, Cell } from 'recharts'

interface ChartData {
  month: string
  value: number
  savings: number
}

export function SavingsChart({ data }: { data: ChartData[] }) {
  return (
    <div className="h-64">
      <ResponsiveContainer width="100%" height="100%">
        <AreaChart data={data}>
          <XAxis dataKey="month" stroke="#888" fontSize={10} />
          <YAxis stroke="#888" fontSize={10} />
          <Tooltip
            contentStyle={{
              backgroundColor: '#080808',
              border: '1px solid #333',
              color: '#E8E8E8',
            }}
          />
          <Area
            type="monotone"
            dataKey="value"
            stroke="#888"
            fill="#333"
            name="Total Value"
          />
          <Area
            type="monotone"
            dataKey="savings"
            stroke="#00C853"
            fill="#00C85333"
            name="Savings"
          />
        </AreaChart>
      </ResponsiveContainer>
    </div>
  )
}

export function ClassificationChart({ data }: { data: { chapter: string; count: number; percentage: number }[] }) {
  const COLORS = ['#080808', '#333333', '#555555', '#777777', '#999999', '#BBBBBB']

  return (
    <div className="h-64">
      <ResponsiveContainer width="100%" height="100%">
        <PieChart>
          <Pie
            data={data}
            cx="50%"
            cy="50%"
            innerRadius={60}
            outerRadius={80}
            dataKey="count"
            nameKey="chapter"
            label={({ chapter, percentage }) => `${percentage}%`}
          >
            {data.map((_, index) => (
              <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
            ))}
          </Pie>
          <Tooltip />
        </PieChart>
      </ResponsiveContainer>
    </div>
  )
}

export function MonthlyTrendChart({ data }: { data: { month: string; classifications: number; shipments: number }[] }) {
  return (
    <div className="h-64">
      <ResponsiveContainer width="100%" height="100%">
        <BarChart data={data}>
          <XAxis dataKey="month" stroke="#888" fontSize={10} />
          <YAxis stroke="#888" fontSize={10} />
          <Tooltip
            contentStyle={{
              backgroundColor: '#080808',
              border: '1px solid #333',
              color: '#E8E8E8',
            }}
          />
          <Bar dataKey="classifications" fill="#080808" name="Classifications" />
          <Bar dataKey="shipments" fill="#555555" name="Shipments" />
        </BarChart>
      </ResponsiveContainer>
    </div>
  )
}
```

#### Complete Analytics Page
```tsx
// src/app/(dashboard)/analytics/page.tsx
'use client'
import { useState, useRef } from 'react'
import { AppLayout } from '@/components/layout/AppLayout'
import { WorkspaceHeader } from '@/components/layout/WorkspaceHeader'
import { ExportButtons } from '@/components/features/analytics/ExportButtons'
import { SavingsChart, ClassificationChart, MonthlyTrendChart } from '@/components/features/analytics/SavingsChart'
import { ResultCard } from '@/components/ui/ResultCard'

const mockData = {
  summary: {
    total_classifications: 847,
    total_shipments: 124,
    total_value_usd: 2340000,
    duties_paid_usd: 187200,
    duties_saved_usd: 42350,
    compliance_score: 94,
  },
  chapters: [
    { chapter: 'Chapter 85 - Electrical', count: 312, percentage: 36.8 },
    { chapter: 'Chapter 84 - Machinery', count: 198, percentage: 23.4 },
    { chapter: 'Chapter 39 - Plastics', count: 145, percentage: 17.1 },
    { chapter: 'Chapter 73 - Iron/Steel', count: 89, percentage: 10.5 },
    { chapter: 'Other', count: 103, percentage: 12.2 },
  ],
  monthly: [
    { month: 'Sep', classifications: 620, shipments: 82, value: 1500000, savings: 28000 },
    { month: 'Oct', classifications: 680, shipments: 95, value: 1750000, savings: 32000 },
    { month: 'Nov', classifications: 720, shipments: 102, value: 1900000, savings: 35000 },
    { month: 'Dec', classifications: 790, shipments: 115, value: 2100000, savings: 38000 },
    { month: 'Jan', classifications: 820, shipments: 120, value: 2250000, savings: 40000 },
    { month: 'Feb', classifications: 847, shipments: 124, value: 2340000, savings: 42350 },
  ],
  exportData: [
    { hsCode: '8504.40.95', count: 156, confidence: 94, savings: 12400 },
    { hsCode: '8518.30.20', count: 89, confidence: 97, savings: 8200 },
    { hsCode: '7323.93.00', count: 67, confidence: 99, savings: 5100 },
  ],
}

export default function AnalyticsPage() {
  const [period, setPeriod] = useState('30d')
  const chartRef = useRef<HTMLDivElement>(null)

  return (
    <AppLayout
      rightPanel={
        <>
          <div className="flex justify-between items-center pb-6 border-b border-[#333]">
            <div className="label text-text-inv">EXPORT_OPTIONS</div>
          </div>
          <div className="space-y-4 mt-4">
            <ExportButtons data={mockData.exportData} chartRef={chartRef} />
            <div className="text-xs text-[#888]">
              Export your analytics data as PDF, Excel, or CSV for reporting and analysis.
            </div>
          </div>

          <div className="mt-8">
            <div className="label text-[#888] mb-4">AI_ACCURACY</div>
            <ResultCard label="OVERALL">
              <div className="font-pixel text-2xl mt-2">94.2%</div>
            </ResultCard>
            <div className="mt-4 space-y-2">
              <div className="flex justify-between text-xs">
                <span>High Confidence (90%+)</span>
                <span className="font-pixel">98.7%</span>
              </div>
              <div className="flex justify-between text-xs">
                <span>Medium Confidence</span>
                <span className="font-pixel">91.3%</span>
              </div>
              <div className="flex justify-between text-xs">
                <span>Low Confidence</span>
                <span className="font-pixel">72.1%</span>
              </div>
            </div>
          </div>
        </>
      }
    >
      <WorkspaceHeader
        title="ANALYTICS"
        pixelTitle="DASHBOARD"
        metaLabel="PERIOD"
        metaValue={period.toUpperCase()}
      />

      {/* Period Selector */}
      <div className="p-4 bg-canvas border-b border-dark flex gap-2">
        {['7d', '30d', '90d'].map((p) => (
          <button
            key={p}
            onClick={() => setPeriod(p)}
            className={`px-4 py-2 font-pixel text-sm ${
              period === p ? 'bg-dark text-canvas' : 'border border-dark'
            }`}
          >
            {p === '7d' ? 'WEEK' : p === '30d' ? 'MONTH' : 'QUARTER'}
          </button>
        ))}
      </div>

      <div className="flex-1 overflow-y-auto p-6">
        {/* Summary Cards */}
        <div className="grid grid-cols-4 gap-4 mb-8">
          <div className="border border-dark p-4">
            <div className="label">CLASSIFICATIONS</div>
            <div className="font-pixel text-3xl mt-2">{mockData.summary.total_classifications}</div>
          </div>
          <div className="border border-dark p-4">
            <div className="label">SHIPMENTS</div>
            <div className="font-pixel text-3xl mt-2">{mockData.summary.total_shipments}</div>
          </div>
          <div className="border border-dark p-4">
            <div className="label">TOTAL VALUE</div>
            <div className="font-pixel text-3xl mt-2">${(mockData.summary.total_value_usd / 1000000).toFixed(2)}M</div>
          </div>
          <div className="border border-dark p-4 bg-dark text-text-inv">
            <div className="label text-[#888]">DUTIES SAVED</div>
            <div className="font-pixel text-3xl mt-2 text-green-500">
              ${mockData.summary.duties_saved_usd.toLocaleString()}
            </div>
          </div>
        </div>

        {/* Charts Row */}
        <div className="grid grid-cols-2 gap-6 mb-8">
          <div className="border border-dark p-4" ref={chartRef}>
            <div className="label mb-4">VALUE & SAVINGS TREND</div>
            <SavingsChart data={mockData.monthly} />
          </div>
          <div className="border border-dark p-4">
            <div className="label mb-4">CLASSIFICATIONS BY CHAPTER</div>
            <ClassificationChart data={mockData.chapters} />
          </div>
        </div>

        {/* Monthly Trend */}
        <div className="border border-dark p-4">
          <div className="label mb-4">MONTHLY ACTIVITY</div>
          <MonthlyTrendChart data={mockData.monthly} />
        </div>

        {/* Top Chapters List */}
        <div className="mt-6 border border-dark p-4">
          <div className="label mb-4">TOP HS CHAPTERS</div>
          <div className="space-y-2">
            {mockData.chapters.map((ch, i) => (
              <div key={i} className="flex items-center gap-4">
                <div className="w-full bg-[#ddd]">
                  <div
                    className="h-6 bg-dark flex items-center px-2"
                    style={{ width: `${ch.percentage}%` }}
                  >
                    <span className="text-text-inv text-xs font-pixel truncate">{ch.chapter}</span>
                  </div>
                </div>
                <span className="font-pixel text-sm w-16 text-right">{ch.count}</span>
              </div>
            ))}
          </div>
        </div>
      </div>
    </AppLayout>
  )
}
```

#### ExportButtons.tsx (PDF/Excel)
```tsx
'use client'
import jsPDF from 'jspdf'
import autoTable from 'jspdf-autotable'
import * as XLSX from 'xlsx'
import { saveAs } from 'file-saver'
import html2canvas from 'html2canvas'

export function ExportButtons({ data, chartRef }: { data: any[]; chartRef?: React.RefObject<HTMLDivElement> }) {
  const exportPDF = async () => {
    const pdf = new jsPDF()

    // Add title
    pdf.setFontSize(20)
    pdf.text('TradeOptimize AI - Analytics Report', 14, 22)

    pdf.setFontSize(10)
    pdf.text(`Generated: ${new Date().toLocaleString()}`, 14, 30)

    // Add table
    autoTable(pdf, {
      head: [['HS Code', 'Classifications', 'Avg Confidence', 'Savings']],
      body: data.map(d => [d.hsCode, d.count, `${d.confidence}%`, `$${d.savings}`]),
      startY: 40,
    })

    // Add chart if ref provided
    if (chartRef?.current) {
      const canvas = await html2canvas(chartRef.current)
      const imgData = canvas.toDataURL('image/png')
      pdf.addPage()
      pdf.addImage(imgData, 'PNG', 10, 10, 190, 100)
    }

    pdf.save('tradeoptimize-report.pdf')
  }

  const exportExcel = () => {
    const ws = XLSX.utils.json_to_sheet(data)
    const wb = XLSX.utils.book_new()
    XLSX.utils.book_append_sheet(wb, ws, 'Analytics')
    const buf = XLSX.write(wb, { bookType: 'xlsx', type: 'array' })
    saveAs(new Blob([buf]), 'tradeoptimize-report.xlsx')
  }

  const exportCSV = () => {
    const ws = XLSX.utils.json_to_sheet(data)
    const csv = XLSX.utils.sheet_to_csv(ws)
    saveAs(new Blob([csv], { type: 'text/csv' }), 'tradeoptimize-report.csv')
  }

  return (
    <div className="flex gap-2">
      <button onClick={exportPDF} className="border border-dark px-3 py-2 text-xs font-pixel hover:bg-dark hover:text-canvas">
        📄 PDF
      </button>
      <button onClick={exportExcel} className="border border-dark px-3 py-2 text-xs font-pixel hover:bg-dark hover:text-canvas">
        📊 EXCEL
      </button>
      <button onClick={exportCSV} className="border border-dark px-3 py-2 text-xs font-pixel hover:bg-dark hover:text-canvas">
        📋 CSV
      </button>
    </div>
  )
}
```

### 4. Error Boundary

```tsx
// src/components/ErrorBoundary.tsx
'use client'
import { Component, ReactNode } from 'react'

interface Props { children: ReactNode }
interface State { hasError: boolean; error?: Error }

export class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="flex items-center justify-center h-full p-8">
          <div className="text-center max-w-md">
            <div className="font-pixel text-2xl mb-4 text-warning">SYSTEM_ERROR</div>
            <p className="text-sm opacity-70 mb-6">
              Something went wrong. Please try refreshing the page.
            </p>
            <button
              onClick={() => window.location.reload()}
              className="bg-dark text-canvas px-6 py-3 font-pixel"
            >
              RELOAD_PAGE
            </button>
          </div>
        </div>
      )
    }
    return this.props.children
  }
}
```

### 5. Empty State

```tsx
// src/components/ui/EmptyState.tsx
interface Props {
  title: string
  description: string
  action?: { label: string; onClick: () => void }
}

export function EmptyState({ title, description, action }: Props) {
  return (
    <div className="flex flex-col items-center justify-center p-12 text-center">
      <div className="w-16 h-16 border-2 border-dashed border-[#555] flex items-center justify-center mb-4">
        <span className="text-3xl opacity-30">∅</span>
      </div>
      <div className="font-pixel text-lg mb-2">{title}</div>
      <p className="text-sm opacity-60 max-w-sm">{description}</p>
      {action && (
        <button
          onClick={action.onClick}
          className="mt-6 bg-dark text-canvas px-6 py-3 font-pixel text-sm"
        >
          {action.label}
        </button>
      )}
    </div>
  )
}
```

### 6. Wrap App in Error Boundary

```tsx
// src/app/(dashboard)/layout.tsx
import { ErrorBoundary } from '@/components/ErrorBoundary'
import { ToastContainer } from '@/components/ui/Toast'

export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <ErrorBoundary>
      {children}
      <ToastContainer />
    </ErrorBoundary>
  )
}
```

---

## Commit Schedule

```bash
# Hour 9:30
git commit -m "feat(fe4): route optimizer page with route cards"

# Hour 10:00
git commit -m "feat(fe4): cargo tracking page with timeline"

# Hour 10:30
git commit -m "feat(fe4): analytics dashboard with charts"

# Hour 11:00
git commit -m "feat(fe4): PDF/Excel/CSV export functionality"

# Hour 11:30
git commit -m "feat(fe4): error boundaries and empty states"

# Hour 12:00
git commit -m "feat(fe4): final polish and responsive tweaks"
git push -u origin feature/fe-phase-4-polish-extras
```

---

## Testing Checklist

- [ ] Route optimizer shows 3 route options
- [ ] Recommended route is highlighted
- [ ] Cargo tracking timeline displays correctly
- [ ] Analytics page shows charts
- [ ] PDF export generates valid document
- [ ] Excel export opens in spreadsheet app
- [ ] Error boundary catches errors gracefully
- [ ] Empty states display when no data

---

## PR Template

```markdown
## Phase FE-4: Polish + Extras

### Changes
- Route Optimizer page with route comparison cards
- Cargo Tracking page with timeline visualization
- Analytics Dashboard with Recharts
- PDF/Excel/CSV export functionality
- Error boundaries for graceful error handling
- Empty state components
- Final UI polish

### Screenshots
[Add screenshots of all new pages + export example]

### Testing
- [x] All pages load without errors
- [x] Export functions work
- [x] Error boundary catches errors

### Dependencies
Requires FE-3 merged
```

---

**Frontend Complete! Coordinate with Backend team for integration.**
