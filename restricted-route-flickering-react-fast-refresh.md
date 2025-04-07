# Preventing Route Flickering in Protected React Routes

## The Problem
When implementing protected routes in React, a common issue is the momentary "flickering" of protected content before redirecting unauthorized users. This occurs because the component initially mounts and renders before the authentication check completes.

## Root Cause
In the problematic implementation below, the component renders immediately while the authentication check happens in `useEffect`:

```typescript
const withAuth = <P extends object>(WrappedComponent: React.ComponentType<P>) => {
  const AuthComponent = (props: P) => {
    const navigate = useNavigate();
    const isAuthenticated = !!localStorage.getItem("token");

    useEffect(() => {
      if (!isAuthenticated) {
        navigate("/login");
      }
    }, [isAuthenticated, navigate]);  

    return <WrappedComponent {...props} />; // Renders immediately!
  };
  return AuthComponent;
};
```

## Solution
To prevent flickering, we implement two key improvements:
1. Add a loading state (`isChecking`)
2. Conditionally render the component only after authentication is verified

Here's the improved implementation:

```typescript
const withAuth = <P extends object>(WrappedComponent: React.ComponentType<P>) => {
  const AuthComponent = (props: P) => {
    const navigate = useNavigate();
    const [isChecking, setIsChecking] = useState(true);
    const isAuthenticated = !!localStorage.getItem("token");

    useEffect(() => {
      if (!isAuthenticated) {
        navigate("/login");
      }
      setIsChecking(false);
    }, [isAuthenticated, navigate]);  

    if (isChecking) {
      return null; // Or a loading spinner
    }
    
    return isAuthenticated ? <WrappedComponent {...props} /> : null;
  };
  return AuthComponent;
};
```

## Key Improvements
- `isChecking` state prevents premature rendering
- Double protection with both `isChecking` and `isAuthenticated` checks
- Clean handling of unauthorized state
- No flickering of protected content

## Usage
```typescript
// Protect any component
const ProtectedDashboard = withAuth(Dashboard);

// Use in routes
<Route path="/dashboard" element={<ProtectedDashboard />} />
```

