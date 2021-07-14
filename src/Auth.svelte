<script>
  import firebase from 'firebase/app';
  import 'firebase/auth';

  const tenant_id = 'clienta-vingz';
  // const tenant_id = 'clientb-cx84w';
  const auth = firebase.auth().tenantId;
  auth.tenantId = tenant_id;

  // Firebase user
  let user = null;

  // small mapper function
  const userMapper = (claims) => ({
    id: claims.user_id,
    name: claims.name,
    email: claims.email,
    picture: claims.picture,
  });

  export const loginWithEmailPassword = (email, password) =>
    auth.signInWithEmailAndPassword(email, password);

  export const logout = () => auth.signOut();

  // will be fired every time auth state changes
  auth.onAuthStateChanged(async (fireUser) => {
    if (fireUser) {
      // in here you might want to do some further actions
      // such as loading more data, etc.

      // if you want to set custom claims such as roles on a user
      // this is how to get them because they will be present
      // on the token.claims object
      const token = await fireUser.getIdTokenResult();
      user = userMapper(token.claims);
    } else {
      user = null;
    }
  });

  // reactive helper variable
  $: loggedIn = user !== null;
</script>

<!-- we will expose all required methods and properties on our slot -->
<div>
  <slot {user} {loggedIn} {logout} />
</div>
