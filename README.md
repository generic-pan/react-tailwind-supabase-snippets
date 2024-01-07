# Supabase Snippets

# Setting Supabase Trigger for Each New User
```
create function public.handle_new_user()
returns trigger
language plpgsql
security definer set search_path = public
as $$
begin
  insert into public.{{NAME_OF_TABLE}} ({{ACCOUNT_ID_COLUMN}})
  values (new.id);
  return new;
end;
$$;

-- Trigger to execute the function after a new user is created
create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();
```

# Allowing Supabase RLS - Each User Can Edit Their Own
```
create policy "User can manage their own subscriptions." on subscriptions for all to authenticated using (auth.uid () = account_id)
with
  check (auth.uid () = account_id);
```

# Connecting with client JS (pastable)
```
import { createClient } from '@supabase/supabase-js'; // Import Supabase client
const supabase = createClient(process.env.REACT_APP_SUPABASE_URL, process.env.REACT_APP_SUPABASE_KEY);

async function getMyInfo(table) {
    try {
        const { data, error } = await supabase.from(table).select('*');
        if (error) throw error;
        return data[0];
    }
    catch (error) {
        console.log(error);
    }
}

async function upsertMyInfo(table, data) {
    try {
        const { error } = await supabase.from(table).upsert(data);
        if (error) throw error;
    }
    catch (error) {
        console.log(error);
    }
}

async function hasMyInfo(table) {
    try {
        const { data, error } = await supabase.from(table).select('*');
        if (error) throw error;
        return data.length > 0;
    }
    catch (error) {
        console.log(error);
    }
}
```

# Display my information
```
import { useEffect, useState } from "react"

const [profileData, setProfileData] = useState({ city: 'N/A', org_type: 'N/A' });
useEffect(() => {
    async function fetchProfileData() {
        const data = await getMyInfo("profiles");
        setProfileData(data);
    }
    fetchProfileData();
}, []);


<div><span className="font-bold">City:</span> {profileData.city}</div>
<div className="mt-1"><span className="mt-3 font-bold">Organization Type:</span> {profileData.org_type}</div>
```
